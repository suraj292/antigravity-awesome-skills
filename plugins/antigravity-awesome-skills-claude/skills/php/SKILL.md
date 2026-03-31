---
name: php
description: "Dedicated PHP engineering skill for building, refactoring, and reviewing modern PHP applications with clean architecture, strict typing, security, and performance in mind."
risk: safe
source: community
date_added: "2026-03-31"
---

# PHP

## Use This Skill When

- Building or refactoring PHP applications
- Reviewing PHP code quality and maintainability
- Designing APIs or backend services in PHP
- Improving performance, reliability, or security in PHP code

## Do Not Use This Skill When

- The stack is not PHP
- The task is framework-specific and should use a narrower skill (for example Laravel-only decisions)

## Engineering Guidelines

- Use strict types and explicit return types where possible
- Prefer clear, testable functions over hidden side effects
- Keep controllers/entry points thin and move logic to services
- Validate and sanitize external input at boundaries
- Use prepared statements/ORM protections to avoid injection vulnerabilities
- Avoid premature abstraction; optimize once bottlenecks are confirmed
- Prefer standard library and language-native features before adding dependencies

## Delivery Expectations

- Production-ready code that is readable and maintainable
- Sensible folder and namespace organization
- Defensive error handling with meaningful exceptions/messages
- Practical tests for critical behavior and edge cases
- Notes on tradeoffs when multiple implementation paths are viable
