---
name: laravel
description: "Dedicated Laravel skill for production-grade application work covering Laravel 8, 9, 10, and 11+. Includes architecture, Eloquent, APIs, auth, queues, events, testing, and per-version guidance."
risk: safe
source: community
date_added: "2026-03-31"
---

# Laravel

## Use This Skill When

- Implementing or refactoring Laravel features (Laravel 8 / 9 / 10 / 11+)
- Building controllers, services, policies, jobs, form requests, and API resources
- Designing validation, authorization, and API error responses
- Tuning Eloquent queries, relationships, caching, and queue workflows
- Setting up authentication, authorization, events, listeners, and notifications
- Writing tests with PHPUnit / Pest for Laravel applications

## Do Not Use This Skill When

- The project is not Laravel-based
- The task is pure PHP with no Laravel runtime (use `php` skill instead)
- The Laravel version is below 8 (flag the version and advise upgrade path)

---

## Version Reference

| Version | PHP Min | Key Additions |
|---|---|---|
| Laravel 8 | PHP 7.3+ | Model factories rewrite, route caching improvements, job batching, dynamic Blade components |
| Laravel 9 | PHP 8.0+ | Symfony 6 foundation, `enum` casts, `->sole()`, implicit route bindings with enums |
| Laravel 10 | PHP 8.1+ | Native type declarations throughout, `Process` facade, `Str::password()`, `invade()` helper |
| Laravel 11 | PHP 8.2+ | Slimmed `bootstrap/app.php`, `Model::shouldBeStrict()`, `Schedule` in `routes/console.php`, per-route middleware |

> **Always confirm the project's Laravel version before suggesting version-specific APIs.**

---

## Architecture Principles

### Layered Structure

```
app/
  Http/
    Controllers/     # Thin — delegate immediately to services
    Requests/        # FormRequest classes for all input validation
    Resources/       # API Resource transformers
    Middleware/
  Models/            # Eloquent models — relationships, scopes, casts only
  Services/          # Business logic and orchestration
  Actions/           # Single-purpose action classes (optional alternative to services)
  Repositories/      # Data access abstraction when needed
  Jobs/              # Queue-dispatched work
  Events/            # Domain events
  Listeners/         # Event handlers
  Policies/          # Model-based authorization
  Notifications/     # Email, SMS, Slack notifications
  Exceptions/        # Custom exception types + handler customization
```

### Controller Rules

- Controllers must be **thin** — validate input, call a service, return a response
- Use **resource controllers** (`php artisan make:controller --resource`) for CRUD operations
- Do not put Eloquent queries or business logic directly in controllers
- Return `JsonResource` or `ResourceCollection` for all API responses — never raw model instances

### Validation

- Always use **FormRequest** classes for complex validation — never inline `$request->validate()` for more than trivial cases
- Add custom rules via `Rule::` helpers or dedicated `Rule` objects
- Keep authorization (`authorize()`) inside FormRequest for per-endpoint access control
- Return structured 422 validation errors from API routes automatically via FormRequest

---

## Eloquent and Database

- Always define `$fillable` or `$guarded` on every model — never leave both empty
- Prevent **N+1 queries** with `with()`, `load()`, or `loadMissing()` eager loading
- Use **query scopes** (`scopeActive()`, `scopeForUser()`) to encapsulate reusable filter logic
- Use **Eloquent casts** for automatic type conversion:
  - Laravel 8/9: `$casts = ['settings' => 'array']`
  - Laravel 9+: `$casts = ['status' => StatusEnum::class]` (enum casting)
  - Laravel 10+: `$casts = ['meta' => AsArrayObject::class]`
- Use **database transactions** (`DB::transaction()`) for any multi-step write operations
- Use **chunking** (`chunk()` / `chunkById()`) for processing large result sets
- Prefer `->withCount()` over loading full relationships just for aggregate values
- Use `->sole()` (Laravel 9+) when exactly one result is expected
- Use **model factories** (Laravel 8+ rewrite with `HasFactory` trait) for seeding and tests

---

## API Development

- Standardize all JSON responses via `JsonResource` / `ResourceCollection`
- Use consistent HTTP status codes: 200, 201, 204, 400, 401, 403, 404, 422, 500
- Apply `throttle:api` middleware to all API routes
- Use `simplePaginate()` or `cursorPaginate()` (Laravel 8+) over `paginate()` for large sets
- Version APIs with route prefix (`/api/v1/`) and separate route files

---

## Authentication and Authorization

- Use **Laravel Sanctum** (included by default from Laravel 8+) for SPA and API token auth
- Use **Laravel Passport** only for full OAuth2 server functionality
- Apply **Policies** for model-level authorization; use `Gate::define()` for non-model checks
- Never check roles/permissions directly in controllers — always delegate to policies or gates
- Use `can` middleware on routes for early rejection

---

## Queues and Jobs

- Offload slow operations (email, external API calls, image processing) to queued **Jobs**
- Implement `ShouldBeUnique` on jobs that must not run concurrently for the same resource
- Use **job batching** (`Bus::batch()`) — available from Laravel 8+
- Define `$tries`, `$backoff`, and `$timeout` on every job class
- Configure the `failed_jobs` table for visibility into failures

---

## Events and Listeners

- Use domain **Events** to decouple side-effects from core business logic
- Register event-listener pairs in `EventServiceProvider`
- Use `ShouldQueue` on listeners for non-blocking side-effects

---

## Caching

- Cache expensive Eloquent queries or computed values with `Cache::remember()`
- Use **cache tags** (Redis / Memcached) for grouped invalidation
- Set appropriate TTLs — never cache indefinitely without an invalidation strategy

---

## Security and Reliability

- Block **mass assignment** vulnerabilities — always set explicit `$fillable` lists
- Never return sensitive fields (passwords, tokens) in API resources
- Use `Hash::make()` for all password storage — never MD5/SHA1
- Use signed URLs (`URL::signedRoute()`) for temporary secure links
- Add **rate limiting** on auth endpoints (`RateLimiter::for('login', ...)`)
- Use `.env` for all environment-specific configuration — never commit `.env`
- Log with structured context arrays: `Log::info('message', ['key' => $value])`

---

## Testing

- **Laravel 8/9**: use PHPUnit with `RefreshDatabase` trait
- **Laravel 10/11**: PHPUnit or Pest (Pest preferred for expressiveness)
- Test categories:
  - **Feature tests**: hit routes end-to-end with `actingAs()`, assert JSON responses
  - **Unit tests**: test services, actions, and value objects in isolation
- Use **factories** for all model test data — never hand-seed test databases
- Assert HTTP responses with `assertStatus()`, `assertJsonStructure()`, `assertJsonPath()`
- Test authorization: assert 403 for unauthorized, 401 for unauthenticated
- Use `Queue::fake()`, `Mail::fake()`, `Event::fake()`, `Notification::fake()` to avoid real I/O
- Cover all critical paths: auth, writes, state transitions, business rules

---

## Laravel 8 / 9 Specific Notes

- **Model factories** (L8+): use `UserFactory::new()->create()` — class-based factories replace `factory()` helper
- **Job batching** (L8+): `Bus::batch([new Job1, new Job2])->dispatch()`
- **Dynamic Blade components** (L8+): `<x-component-name />` syntax
- **Route caching**: always run `php artisan route:cache` in production
- **Enum casts** (L9+): cast model attributes directly to backed PHP enums
- **Implicit enum binding** (L9+): route model binding resolved directly to enum values

## Laravel 10 Specific Notes

- Native type declarations added across the entire framework source
- `Process` facade for running shell commands: `Process::run('ls -la')`
- `Str::password()` for generating secure random passwords
- Invade helper (`invade($object)->privateProperty`) for testing private state

## Laravel 11 Specific Notes

- Slimmed `bootstrap/app.php` — configure middleware, routing, exceptions fluently
- `Model::shouldBeStrict()` in `AppServiceProvider` — detects lazy loading and missing attributes in non-production
- `Schedule` defined in `routes/console.php` — no `Kernel.php` required
- `Limit::perMinute()` in `RateLimiter` for named rate limits

---

## Output Style

- Provide complete, production-ready implementations (model + migration + FormRequest + service + resource + controller + test)
- Always note which Laravel version a specific API or pattern requires
- Use strict typing and full return type declarations in all generated code
- Follow PSR-12 coding style
- Include migrations when schema changes are involved
- Document tradeoffs briefly when choosing between patterns (e.g. Service vs Action, Sanctum vs Passport)
