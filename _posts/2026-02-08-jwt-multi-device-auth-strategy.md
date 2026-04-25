---
layout: post
title: "ADR-0005: Designing a Multi-Device JWT Strategy - Balancing Statelessness and Control"
date: 2026-02-08
categories: [Backend, Security, Architecture]
tags: [JWT, Spring Security, Authentication, Database]
status: Accepted
project_id: blog
decision_makers: ktaekim
description: "How to manage concurrent user sessions and force logout in a stateless JWT environment using a 1:N Refresh Token strategy."
---

## 1. Context & Problem

JWT (JSON Web Token) is widely praised for its **statelessness**. By embedding all user information in the token itself, we eliminate the need for server-side session lookups, which is ideal for scaling microservices.

However, statelessness introduces a critical **Control Gap**:
*   **The Revocation Problem:** If a token is stolen or a user loses their phone, the server cannot "kill" that token until it naturally expires.
*   **The Multi-Device Requirement:** Modern users expect to stay logged in on their laptop and mobile simultaneously. But if they suspect an unauthorized login, they must be able to "Logout all devices" or revoke access for a specific device.

In the **MICE Forum CMS** project, I needed to support multi-device logins while ensuring the ability to revoke specific sessions.

---

## 2. Options Considered

### Option A: Pure Stateless JWT
- Short-lived Access Tokens, no Refresh Tokens stored.
- **Pros:** Maximum scalability, zero DB hits.
- **Cons:** No way to force logout. If a token is stolen, the attacker has a window of access.

### Option B: Redis-backed Blacklist
- Access Tokens are stateless, but if a user logs out, the token is added to a Redis "Blacklist" until it expires.
- **Pros:** Real-time revocation.
- **Cons:** Every single API request now requires a Redis lookup to check the blacklist, effectively making the system stateful again.

### Option C: 1:N Stateful Refresh Tokens (Hybrid)
- **Access Tokens:** Very short-lived (e.g., 15 mins), 100% stateless.
- **Refresh Tokens:** Long-lived (e.g., 7 days), **stored in a Database/Redis** with a mapping to the user and device.
- **Pros:** Best of both worlds. Standard requests stay stateless. Revocation happens only during the "Refresh" cycle or via an explicit delete.
- **Cons:** Requires a database table and a cleanup job.

---

## 3. Decision & Rationale

I chose **Option C: 1:N Stateful Refresh Tokens**.

### Rationale:
1.  **Security & Control:** By storing Refresh Tokens in the database with a `device_info` column, the server regains control. To force a logout, we simply delete the specific Refresh Token from the DB.
2.  **Scalability:** 99% of user interactions use the stateless Access Token. We only hit the database once every 15 minutes (when refreshing), minimizing the I/O bottleneck compared to a blacklist approach.
3.  **User Experience:** Supporting multiple Refresh Tokens per user allows a seamless experience across multiple devices without one device's login invalidating another's.

---

## 4. Implementation Details

I designed a dedicated table `nt_refresh_tokens` to manage the relationship.

### Data Schema (PostgreSQL)
```sql
CREATE TABLE nt_refresh_tokens (
    token_seq SERIAL PRIMARY KEY,
    member_seq BIGINT NOT NULL REFERENCES nt_member(member_seq),
    refresh_token VARCHAR(255) NOT NULL,
    device_info VARCHAR(255), -- e.g., "Chrome/Windows", "iPhone/App"
    issued_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL
);
```

### The Refresh Logic
When the client presents a Refresh Token:
1.  Check if the token exists in `nt_refresh_tokens`.
2.  Validate the expiration date.
3.  If valid, issue a new Access Token.
4.  **Security Rotation (Optional):** Issue a *new* Refresh Token and replace the old one in the DB to prevent replay attacks.

### Cleanup Strategy
To prevent the table from growing indefinitely, I implemented a Spring Boot `@Scheduled` task to prune expired tokens daily.

```java
@Scheduled(cron = "0 0 3 * * ?") // 3 AM every day
public void cleanExpiredTokens() {
    refreshTokenRepository.deleteByExpiresAtBefore(LocalDateTime.now());
}
```

---

## 5. Trade-offs & Risks

### Trade-offs:
*   **Consistency Lag:** If a user is "Force Logged Out," they can still access the system until their current 15-minute Access Token expires. 
    *   *Decision:* For our business context, a 15-minute lag was an acceptable trade-off for the performance gained by not checking a blacklist on every request.

### Risks:
*   **Database Growth:** In high-traffic systems, this table can become massive.
    *   *Mitigation:* Use **Redis** instead of a RDBMS if the number of concurrent sessions exceeds 1 million. For our project, PostgreSQL was more than sufficient.

---

## 6. Things to Consider

### 1. Security vs. Performance
- If we need **instant** revocation (e.g., for a high-security banking app), we *must* use a blacklist (Option B). 
- For most social or enterprise apps, the **Refresh Token Rotation** strategy (Option C) provides the right balance.

### 2. Device Fingerprinting
- How do we uniquely identify a "device"? 
- Using simple `User-Agent` strings can be collision-prone. In a mobile-first environment, using a unique Device ID provided by the OS is safer.
