---
name: vuejs
description: "Dedicated Vue.js skill for building and maintaining Vue 3 applications with Composition API, reactivity best practices, component architecture, routing, state management, and performance optimization."
risk: safe
source: community
date_added: "2026-03-31"
---

# Vue.js

## Use This Skill When

- Building features in Vue 3 applications
- Creating or refactoring components and composables
- Designing state management, routing, and async data flows
- Improving Vue app performance and maintainability

## Do Not Use This Skill When

- The frontend stack is not Vue
- The task is framework-agnostic UI guidance only

## Vue Engineering Principles

- Prefer Composition API and composables for reusable logic
- Keep components focused and avoid bloated single-file components
- Use explicit props/events contracts and avoid implicit coupling
- Derive state when possible instead of duplicating reactive data
- Keep watchers intentional and cleanup side effects correctly
- Co-locate tests with components/composables for critical behavior

## Performance and UX

- Lazy-load route-level chunks when appropriate
- Avoid unnecessary reactive depth for large data structures
- Use keyed lists correctly and prevent expensive re-renders
- Handle loading, empty, and error states explicitly

## Delivery Expectations

- Clear component boundaries and predictable data flow
- Production-ready Vue 3 patterns (script setup where suitable)
- Readable templates and composables with strong naming
- Actionable notes for follow-up improvements when relevant
