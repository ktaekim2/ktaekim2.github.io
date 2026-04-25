---
layout: post
title: "ADR-0047: API Testing Strategy Baseline with MockMvc Web Slice"
date: 2026-03-03
status: Accepted
project_id: ecp-platform
career_relevance: high
tech_stack: [Spring Boot, Spring MVC, MockMvc, JUnit 5, Spring Test]
decision_makers: ktaekim
---

# ADR-0047: API Testing Strategy Baseline with MockMvc Web Slice

## Status
Accepted  
Date: 2026-03-03

## Context
The current project is a Spring Boot REST API built on Spring MVC (not WebFlux).

Until now, API behaviour has mainly been checked with manual Postman tests.
This created recurring issues:
- repetitive regression checks were slow
- exception-path checks were easily missed
- refactoring safety was hard to verify automatically

Options reviewed:
- full TDD adoption
- MockMvc-based web slice tests
- WebTestClient
- Rest Assured integration tests
- TestContainers-based environment integration tests

Current project constraints:
- Spring MVC stack
- limited DB-specific feature usage
- low external infrastructure dependency
- need to keep development speed high

## Decision
Adopt MockMvc-based web slice tests as the default API testing strategy for this project.

Scope of this decision:
- Use MockMvc tests as the primary automated guard for controller request/response contracts.
- Do not adopt full TDD by default; apply TDD incrementally only when domain logic becomes complex.
- Do not introduce WebTestClient, Rest Assured, or TestContainers at the current stage.

## Rationale
- MockMvc validates controller behaviour quickly without running a servlet container.
- It is sufficient for preventing API contract regressions in the current project phase.
- WebTestClient has limited advantage in a non-WebFlux stack.
- Current integration requirements for DB-specific behaviour and external infrastructure are low.
- TestContainers would increase CI time and pipeline complexity at this stage.
- Full TDD-by-default can reduce early delivery speed in the current team context.

## Consequences
### Positive
- API contract checks become automated.
- Dependency on manual Postman regression checks is reduced.
- Refactoring safety improves.
- Fast execution is feasible in CI.

### Negative
- TDD design benefits are used only selectively.
- Network/real DB environment differences may be detected later than full integration approaches.

## Future Considerations
Revisit this strategy when one or more conditions appear:
- increase in DB-specific query/index issues
- introduction of external infrastructure such as Kafka/Redis
- migration to WebFlux
- stronger need to validate API security/filter-chain integration end-to-end

## Interview One-liner
Established a conservative API test baseline by standardising on MockMvc web slice tests, reducing manual regression effort while keeping CI execution fast for a Spring MVC project.
