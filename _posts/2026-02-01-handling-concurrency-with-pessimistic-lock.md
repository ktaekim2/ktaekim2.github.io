---
layout: post
title: "ADR-0004: Solving Race Conditions in Reservation Systems"
date: 2026-02-01
categories: [Backend, Database, Concurrency]
tags: [Java, Spring Boot, JPA, QueryDSL]
status: Accepted
decision_makers: ktaekim
description: "Why I chose Pessimistic Locking over Optimistic Locking to ensure data integrity in a MICE forum reservation system."
---

## 1. Context & Problem

In the **MICE Forum CMS** project, I was tasked with building a reservation system for limited-seat conference sessions.
The core challenge was handling **Race Conditions**.

**The Scenario:**
*   A session has only **1 seat** remaining.
*   User A and User B click the "Book" button at the exact same millisecond.
*   Both requests pass the initial check (`if seats > 0`), and the database records two reservations.
*   **Result:** Overbooking (Seats: -1).

For a reservation system, **Data Integrity** is non-negotiable. Overbooking leads to operational chaos and angry customers. I needed a mechanism to serialize these concurrent requests.

---

## 2. Options Considered

### Option A: Application-Level Locking (`synchronized`)
- Use Java's `synchronized` keyword or `ReentrantLock` to serialize access to the booking method.
- **Pros:** Easiest to implement. No DB schema changes.
- **Cons:** **Fails in a distributed environment.** If the server scales out to 2+ instances, locks on Server A cannot stop requests on Server B.
- **Verdict:** Rejected (Not scalable).

### Option B: Optimistic Locking (`@Version`)
- Use a version column in the database. If the version changes between read and write, throw an exception.
- **Pros:** Non-blocking. High performance when conflicts are rare.
- **Cons:** **Poor UX in high-concurrency.** If 10 people try to book 1 seat, 1 succeeds and 9 get an error ("Data modified, please retry"). Asking users to retry manually is frustrating in a booking context.
- **Verdict:** Rejected (UX concerns).

### Option C: Pessimistic Locking (`SELECT ... FOR UPDATE`)
- Lock the database row immediately upon reading. Other transactions must wait until the lock is released.
- **Pros:** **Guaranteed consistency.** Serialization is enforced at the DB level, working across all server instances. From the user's perspective, the request just "waits" slightly rather than failing immediately.
- **Cons:** Performance penalty. Potential for deadlocks if not managed carefully.
- **Verdict:** **Accepted.**

---

## 3. Decision & Rationale

I chose **Option C: Pessimistic Locking**.

**Why?**
1.  **Business Priority:** In a reservation system, **"First-Come, First-Served"** fairness and **Zero Overbooking** are more important than maximizing raw throughput.
2.  **Traffic Characteristics:** The MICE forum expected burst traffic (at opening time) but not "millions of TPS" scale. The overhead of row-level locking was acceptable within our PostgreSQL capacity.
3.  **UX Consistency:** It is better for a request to hang for 200ms and succeed (or return "Full") than to fail instantly with a "Concurrency Error" and force the user to click again.

---

## 4. Implementation Details

I implemented the lock using **QueryDSL** within a Spring Boot transaction.

```java
// Simplified Example
@Transactional
public void reserveSeat(Long sessionId, Long userId) {
    // 1. Acquire Lock (PESSIMISTIC_WRITE -> SELECT ... FOR UPDATE)
    Session session = queryFactory.selectFrom(qSession)
        .where(qSession.id.eq(sessionId))
        .setLockMode(LockModeType.PESSIMISTIC_WRITE) // The Key Logic
        .fetchOne();

    // 2. Validate
    if (session.getRemainingSeats() <= 0) {
        throw new SoldOutException();
    }

    // 3. Process
    session.decreaseSeat();
    reservationRepository.save(new Reservation(session, userId));
    
    // Lock is released when transaction commits
}
```

By setting `LockModeType.PESSIMISTIC_WRITE`, Spring Data JPA issues a `SELECT ... FOR UPDATE` query. This ensures that once a transaction reads the seat count, no other transaction can modify it until the current one finishes.

---

## 5. Trade-offs & Risks

### Trade-off: Latency
*   **Cost:** Requests are serialized. If 100 users try to book simultaneously, the 100th user has to wait for 99 transactions to finish (or timeout).
*   **Mitigation:** Kept the transaction scope **as short as possible**. Only the critical "Check & Decrease" logic is inside the lock. Non-critical logic (e.g., sending email confirmations) was moved outside the transaction or handled asynchronously.

---

## 6. Things to Consider

### 1. What if traffic spikes to millions?
- Pessimistic locking would become a bottleneck (DB CPU spike).
- **Alternative:** For "Taylor Swift Concert" scale, I would switch to **Redis (Lua Script)** or a Queue-based architecture (Kafka) to handle requests asynchronously. But for this MICE project, Redis was architectural over-engineering.

### 2. Deadlocks
- Locking multiple resources (e.g., Seat + User Point) can cause deadlocks.
- **Action:** Enforced a strict **Lock Ordering** policy (always lock 'Session' first, then 'User') to prevent circular dependencies.
