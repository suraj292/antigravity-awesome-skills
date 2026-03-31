---
name: reactjs
description: "Dedicated React.js skill for building and maintaining modern React applications with clear component boundaries, predictable state flow, performance-aware rendering, and robust testing patterns."
risk: safe
source: community
date_added: "2026-03-31"
---

# React.js

## Use This Skill When

- Building new React features or refactoring existing ones
- Designing component boundaries and state ownership
- Implementing async data handling and side-effect management
- Reviewing React code for correctness and performance

## Do Not Use This Skill When

- The project is not React-based
- The request is specific to another framework (for example Vue-only or Angular-only)

## React Engineering Principles

- Keep components small, composable, and responsibility-focused
- Lift state only as far as needed; avoid global state by default
- Model side effects intentionally with clear dependencies
- Prefer derived values over duplicated state
- Keep hooks deterministic and avoid conditional hook calls
- Use semantic HTML and accessibility-first UI patterns

## Performance Guidance

- Prevent unnecessary re-renders via stable props and memoization where it helps
- Split large UI trees and lazy-load heavy boundaries
- Profile before optimizing and document measured bottlenecks
- Use efficient list rendering and stable keys

## Delivery Expectations

- Production-ready React code with predictable behavior
- Clear naming, maintainable file structure, and focused components
- Tests for critical interactions and state transitions
- Brief tradeoff notes when multiple valid patterns exist
