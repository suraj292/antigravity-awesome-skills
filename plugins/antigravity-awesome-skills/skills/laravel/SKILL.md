---
name: laravel
description: "Dedicated Laravel skill for production-grade application work using modern Laravel conventions (10/11+), including architecture, validation, Eloquent, APIs, auth, queues, and testing."
risk: safe
source: community
date_added: "2026-03-31"
---

# Laravel

## Use This Skill When

- Implementing or refactoring Laravel features
- Building controllers, services, policies, jobs, and API resources
- Designing validation, authorization, and API error responses
- Tuning Eloquent queries, caching, and queue workflows

## Do Not Use This Skill When

- The project is not Laravel-based
- The task is pure PHP with no Laravel runtime

## Laravel Standards

- Keep controllers thin and business logic in services/actions
- Use Form Requests for validation and Policy/Gate for authorization
- Use API Resources for response shaping
- Prevent N+1 queries with eager loading
- Use transactions for multi-step data integrity operations
- Use queues for slow/IO-heavy operations
- Respect framework conventions over custom patterns without clear need

## Security and Reliability

- Enforce input validation on every external boundary
- Prevent mass-assignment issues with guarded/fillable strategy
- Never leak sensitive fields in responses
- Use framework auth primitives (Sanctum/guards) when appropriate
- Add tests around critical paths (auth, money, writes, and state transitions)

## Output Style

- Provide pragmatic, maintainable Laravel-native implementations
- Include migration/model/request/controller/service/resource updates as needed
- Document key tradeoffs briefly when choosing between patterns
