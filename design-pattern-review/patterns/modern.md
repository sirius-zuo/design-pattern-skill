# Modern Patterns

Patterns that have emerged beyond the original GoF catalog, common in modern distributed and enterprise systems.

---

## Repository

**When to apply:**
- You want to decouple domain logic from data access logic
- You need to swap storage backends (database, in-memory, file) without changing business code
- Domain objects should not know about persistence mechanisms

**Code smell signals:**
- SQL queries or ORM calls scattered directly in service/business logic classes
- Business logic tests require a real database
- Switching the database would require changes throughout the codebase

**When NOT to apply:**
- The application is primarily a CRUD layer with little domain logic — Repository adds ceremony with no benefit
- The data access is a simple, single-purpose script

**Language considerations:**
- Go: Interface + struct; `sqlx` or `pgx` in concrete implementations; mock the interface in tests
- Java: Spring Data's `JpaRepository`; or plain interface + implementation
- Python: Abstract base class with a concrete SQLAlchemy or Django ORM implementation
- Rust: Trait + async concrete implementation; `sqlx` or `diesel`

---

## Dependency Injection

**When to apply:**
- You want to decouple components from the creation of their dependencies
- You need to swap implementations (e.g., real vs. mock in tests)
- Object graphs are complex and you want a container to manage lifecycle

**Code smell signals:**
- `new Dependency()` deep inside business logic
- Hard-coded service locator calls (`ServiceLocator.get(Foo.class)`)
- Tests that can't run without real databases, networks, or external services

**When NOT to apply:**
- DI frameworks add too much complexity for a small project — constructor injection without a framework is fine
- Overuse leads to deeply nested injection graphs that obscure what a component actually needs

**Language considerations:**
- Java: Spring, Guice, or CDI; constructor injection preferred over field injection
- Python: No framework needed for small projects; `injector` or `dependency-injector` libraries
- Go: Manual constructor injection is idiomatic; `wire` for complex graphs
- Rust: Manual injection; `shaku` crate for DI containers

---

## Circuit Breaker

**When to apply:**
- Calls to external services (HTTP APIs, databases, queues) can fail or become slow
- You want to fail fast when a downstream service is unhealthy, rather than cascading timeouts
- You need automatic recovery when the downstream service comes back

**Code smell signals:**
- Retry loops without backoff that hammer a failing service
- Timeouts set to very long values to "wait for the service to recover"
- Cascading failures that take down the whole system when one dependency is slow

**When NOT to apply:**
- Calling internal in-process functions — no network, no circuit breaker needed
- The operation is idempotent and cheap enough to always retry

**Language considerations:**
- Go: `gobreaker` or `sony/gobreaker`; or implement with atomic state + time
- Java: Resilience4j CircuitBreaker; Netflix Hystrix (legacy)
- Python: `pybreaker` library; or custom state machine
- Rust: `failsafe-rs` or custom implementation

---

## Event Sourcing

**When to apply:**
- The full history of state changes is business-critical (audit log, financial ledger)
- You need to reconstruct past states or replay events
- Domain events are first-class concepts in the system

**When NOT to apply:**
- Simple CRUD applications — event sourcing adds significant complexity
- The team is unfamiliar with eventual consistency and CQRS (usually paired together)
- Storage costs for the event log are prohibitive

**Language considerations:**
- All languages: Event store (append-only log) + projections; often paired with CQRS

---

## CQRS (Command Query Responsibility Segregation)

**When to apply:**
- Read and write workloads have very different performance and scaling requirements
- Complex domain operations need separation from read-optimized query models
- Often paired with Event Sourcing

**Code smell signals:**
- A single model trying to serve both complex writes and many different read views
- Performance problems that can't be solved without separate read/write optimization

**When NOT to apply:**
- Simple CRUD — CQRS adds two models where one would do
- Small teams that would struggle to maintain the additional complexity

**Language considerations:**
- All languages: Separate command handlers and query handlers; separate read/write models

---

## Saga

**When to apply:**
- You need to coordinate a long-running distributed transaction across multiple services
- You can't use a 2-phase commit (microservices, independent databases)
- Each step has a compensating transaction for rollback

**When NOT to apply:**
- Operations are within a single service/database — use a regular transaction
- The workflow is simple enough for a single synchronous call

**Language considerations:**
- All languages: Choreography (events trigger each step) or Orchestration (central coordinator)
- Go: Event-driven with NATS/Kafka; or process manager pattern
- Java: Axon Framework; Spring State Machine

---

## Retry with Backoff

**When to apply:**
- Transient failures in network calls, database connections, or external APIs
- Operations are idempotent (safe to retry)

**Code smell signals:**
- Bare `try/catch` that immediately re-throws on the first failure
- Manual retry loops without delay or max-attempt limits

**When NOT to apply:**
- Operations are not idempotent (double-charging, duplicate records)
- The failure is permanent (400 Bad Request, not 503 Unavailable)

**Language considerations:**
- Go: `github.com/cenkalti/backoff/v4`
- Python: `tenacity` library
- Java: Resilience4j Retry; Spring Retry
- Rust: `tokio-retry` crate

---

## Pub/Sub (Publisher/Subscriber)

**When to apply:**
- Components need to communicate without being directly coupled
- Multiple consumers need to react to the same event independently
- Fanout notifications across services or modules

**Code smell signals:**
- Service A directly calling Service B, C, D when something happens — tight coupling
- Logic to "notify all interested parties" hardcoded in business logic

**When NOT to apply:**
- Only one subscriber exists and it won't change — direct call is simpler and clearer
- Strict ordering or exactly-once delivery is required and your pub/sub system doesn't guarantee it

**Language considerations:**
- Go: Channels; `watermill` for distributed pub/sub
- Python: `asyncio` event system; Celery; Redis pub/sub
- Java: Spring ApplicationEventPublisher; Kafka; RabbitMQ
- Rust: `tokio::sync::broadcast`; message queue clients
