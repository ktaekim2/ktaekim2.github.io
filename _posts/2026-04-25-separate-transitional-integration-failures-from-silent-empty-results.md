---
layout: post
title: "ADR-0074: Separate Transitional Integration Failures from Silent Empty Results"
date: 2026-04-25
status: Accepted
project_id: seculayer
career_relevance: high
tech_stack: [Java, Spring, HTTP, Observability, Legacy Integration, API Design]
decision_makers: ktaekim
---

# ADR-0074: Separate Transitional Integration Failures from Silent Empty Results

## Problem

During a legacy-to-modern platform migration, a new service still had to call a legacy module for part of its data. The integration was in a transitional state: some responses could be temporarily incompatible with the new client's expectations.

The risky part was not only the incompatibility itself. The larger risk was handling all integration failures with a broad `catch (Exception)` block and returning an empty result to the caller.

That behaviour makes the user interface look harmless, but it hides the difference between:

- there is genuinely no data;
- the legacy integration is temporarily incomplete;
- a real production issue has occurred, such as a wrong URL, authentication failure, HTML error page, timeout, proxy issue, or JSON parsing failure.

If these cases are all converted into the same empty response, the system loses operational visibility exactly where migration risk is highest.

## Context

The integration boundary had a strict response validation rule. Even when the HTTP status was `200`, the client rejected the response if the `Content-Type` did not indicate JSON.

That strictness was reasonable. A `200` status with an HTML body, login page, reverse-proxy error, or legacy fallback response should not be treated as valid business data.

However, the upper layer treated the resulting exception as a non-fatal data absence case. It swallowed the exception and continued with an empty payload. This created two conflicting behaviours:

- the low-level client correctly detected an invalid integration response;
- the application layer removed the diagnostic signal before operators could use it.

In a migration period, some transitional failures are expected. But expected does not mean invisible.

## Decision

Handle transitional integration failures explicitly and keep unexpected failures observable.

### 1. Model expected transitional failures as a known compatibility state

If a legacy module is temporarily unavailable, not yet migrated, or known to return a non-standard response, represent that condition explicitly.

Acceptable patterns include:

- a compatibility-mode flag;
- a feature toggle;
- a typed exception such as `LegacyIntegrationNotReadyException`;
- an explicit degraded response that says the integration is temporarily unavailable;
- a warning log with a migration-specific reason code.

The goal is to make the transitional state intentional rather than accidental.

### 2. Do not convert every integration failure into an empty result

An empty list or empty string should mean "the request succeeded and there is no data".

It should not also mean:

- the upstream URL is wrong;
- the upstream returned HTML instead of JSON;
- authentication failed;
- a proxy returned an error page;
- the request timed out;
- the response parser failed.

Those cases should remain visible as operational failures.

### 3. Narrow exception handling at the integration boundary

Instead of catching all exceptions at the application layer, separate expected and unexpected cases:

- expected transitional incompatibility -> `warn` log, explicit degraded response, or feature-flagged empty result;
- unexpected client or protocol failure -> `error` log with diagnostic context;
- programmer or parsing error -> fail visibly and let normal error handling capture it.

This keeps the application tolerant without making it blind.

### 4. Preserve minimum diagnostic context

For unexpected integration failures, logs should include enough context to diagnose the issue without leaking secrets:

- logical integration name;
- endpoint category or operation name;
- HTTP status code, if available;
- response `Content-Type`, if available;
- timeout or parsing failure category;
- correlation ID or request ID, if available;
- whether the request was in compatibility mode.

The log should not include tokens, credentials, full sensitive URLs, or raw response bodies unless they are explicitly sanitised.

### 5. Timebox the transitional path

Compatibility handling should not become permanent architecture by accident.

Every transitional branch should have one of the following:

- a removal condition;
- a migration completion trigger;
- an owner;
- a review date;
- a test that describes why the branch still exists.

## Alternatives Considered

### Option A: Keep swallowing all exceptions and return empty data

Rejected.

This is simple for the UI, but it destroys observability. It also makes "no data" indistinguishable from "the integration is broken".

During migration, that ambiguity is expensive because failures often happen at system boundaries: content type, proxy routing, authentication, version mismatch, and response schema drift.

### Option B: Disable strict content-type validation

Rejected.

Relaxing the client would reduce visible errors, but it would also allow invalid responses to move deeper into the application. A strict integration client is useful because it protects the business layer from treating protocol failures as valid data.

The problem is not strict validation. The problem is losing the signal after validation fails.

### Option C: Fail hard on every transitional mismatch

Partially rejected.

Failing hard is appropriate for unexpected failures, but some migration states are known and temporary. In those cases, a carefully logged degraded response can be acceptable if the product behaviour is intentionally agreed.

The important requirement is that the degraded state must be explicit and observable.

## Implementation Guidance

A practical structure is:

```java
try {
    return legacyClient.fetchData(request);
} catch (LegacyIntegrationNotReadyException e) {
    log.warn("Legacy integration not ready: operation={}, mode={}", operationName, migrationMode);
    return DegradedResult.integrationTemporarilyUnavailable();
} catch (InvalidIntegrationResponseException e) {
    log.error(
        "Invalid integration response: operation={}, status={}, contentType={}",
        operationName,
        e.statusCode(),
        e.contentType()
    );
    throw e;
}
```

The exact class names are less important than the boundary:

- expected migration state is handled intentionally;
- invalid integration response remains visible;
- empty data is not overloaded as an error transport.

## Validation

This decision should be validated with boundary tests around the integration client and service layer:

- valid JSON response -> normal data response;
- valid JSON empty array -> empty data response;
- HTTP `200` with non-JSON content type -> visible integration error;
- HTTP `401` or `403` -> visible authentication/authorisation integration error;
- proxy HTML error page -> visible invalid response error;
- timeout -> visible upstream timeout error;
- known transitional state -> explicit degraded response with `warn` log;
- unexpected exception -> `error` log and normal error propagation.

Operationally, verification should include checking that the expected log appears in the normal application log path, not only in notification channels.

## One-line Summary

During legacy integration migration, expected transitional failures may be degraded intentionally, but unexpected failures must not be hidden behind silent empty results.
