---
name: php
description: "Dedicated PHP engineering skill for building, refactoring, and reviewing modern PHP 8.x applications with clean architecture, strict typing, security, testing, and performance in mind."
risk: safe
source: community
date_added: "2026-03-31"
---

# PHP

## Use This Skill When

- Building or refactoring PHP applications (PHP 8.0+)
- Reviewing PHP code quality, type safety, and maintainability
- Designing APIs or backend services in PHP
- Improving performance, reliability, or security in PHP code
- Implementing OOP patterns, interfaces, and traits
- Working with PHP standard library functions, generators, and streams

## Do Not Use This Skill When

- The stack is not PHP
- The task is framework-specific and should use a narrower skill (e.g. Laravel-only decisions)
- The project uses PHP < 8.0 (flag the version mismatch)

---

## PHP 8.x Feature Usage

Always prefer modern PHP 8.x features over legacy patterns:

| Feature | Use Case |
|---|---|
| `match` expressions | Replace multi-case `switch` statements |
| Named arguments | Improve call-site readability for functions with many parameters |
| Enums (`enum`) | Replace class-based constant groups |
| Constructor property promotion | Reduce boilerplate in value objects and DTOs |
| Union types / intersection types | Express precise type contracts |
| Nullsafe operator (`?->`) | Eliminate repetitive null checks in chains |
| Fibers | Low-level concurrency where applicable |
| `readonly` properties | Enforce immutability in DTOs and value objects |
| `#[Attribute]` | Metadata for validation, routing, serialization |
| `str_contains()`, `str_starts_with()`, `str_ends_with()` | Replace `strpos` hacks |

---

## Engineering Guidelines

### Typing and Safety

- Always declare `declare(strict_types=1)` at the top of every file
- Use explicit return types on all functions and methods (`void`, `string`, `int`, `bool`, `array`, `never`, etc.)
- Use typed properties in all classes — avoid untyped public properties
- Use union types (`int|string`) and intersection types (`Countable&Iterable`) deliberately
- Prefer `readonly` for DTOs and value objects that must not mutate after construction

### Architecture and Design

- Keep entry points (controllers, CLI commands) thin — delegate to services or domain objects
- Use the **Repository pattern** to decouple data access from business logic
- Use **DTOs (Data Transfer Objects)** to pass structured data between layers
- Avoid service locators and prefer constructor injection for all dependencies
- Apply **SOLID principles**: single responsibility, open/closed, Liskov substitution, interface segregation, dependency inversion
- Use interfaces to define contracts between layers; depend on abstractions not concretions
- Prefer small, composable functions over omnibus utility classes

### Error Handling

- Use a **custom exception hierarchy** — extend domain-specific exceptions from a base `AppException`
- Never swallow exceptions silently — log or rethrow with context
- Use `finally` blocks to guarantee resource cleanup (DB connections, file handles)
- Distinguish between **recoverable** (validation, not found) and **unrecoverable** (infrastructure failures) errors

### Security

- Always validate and sanitize user input at the application boundary
- Use `password_hash(PASSWORD_ARGON2ID, ...)` and `password_verify()` — never MD5/SHA1 for passwords
- Use prepared statements or ORM parameterization — never interpolate user input into SQL
- Sanitize output in templates to prevent XSS (`htmlspecialchars()` with `ENT_QUOTES`)
- Avoid `eval()`, `exec()`, `shell_exec()` with user-supplied data
- Store secrets in environment variables — never hard-code credentials
- Validate file uploads: MIME type, size, extension allowlist, and store outside web root

### Performance

- Use **generators** (`yield`) for processing large datasets to avoid loading everything into memory
- Prefer `array_*` functions (`array_map`, `array_filter`, `array_reduce`) for clarity; use plain `foreach` when performance matters
- Cache expensive computations or DB results (APCu for in-process, Redis/Memcached for shared)
- Use `SplFixedArray` or SPL data structures (`SplQueue`, `SplStack`, `SplHeap`) for performance-critical collections
- Profile before optimizing — use Xdebug or Blackfire to confirm real bottlenecks

### Standards and Conventions

- Follow **PSR-1** (basic coding standard), **PSR-2/PSR-12** (coding style), **PSR-4** (autoloading)
- Use Composer for dependency management — pin major versions, review changelogs on upgrades
- Format code with **PHP-CS-Fixer** or **PHP_CodeSniffer** to enforce style
- Use **PHPStan** or **Psalm** (level 8 target) for static analysis in CI

---

## Testing

- Write **PHPUnit** tests for all critical and non-trivial logic
- Use the **Arrange-Act-Assert** pattern for clear test structure
- Test edge cases: empty input, null values, boundary conditions, and error paths
- Use **test doubles** (mocks, stubs, fakes) to isolate units from dependencies
- Separate unit tests (pure logic) from integration tests (DB, HTTP, filesystem)
- Maintain minimum **80% line coverage** on business-critical code

---

## File and Namespace Organization

```
src/
  Domain/         # Business entities, value objects, domain services
  Application/    # Use cases, DTOs, command/query handlers
  Infrastructure/ # DB repositories, external API clients, mailers
  Http/           # Controllers, middleware, request handlers
tests/
  Unit/
  Integration/
  Feature/
```

---

## Delivery Expectations

- Production-ready code with `declare(strict_types=1)` and explicit types throughout
- Sensible PSR-4 namespace and folder organization
- Defensive error handling with meaningful domain exceptions
- Tests covering critical paths and edge cases
- Brief tradeoff notes when choosing between implementation approaches
- No hard-coded credentials, no commented-out dead code, no magic numbers without named constants
