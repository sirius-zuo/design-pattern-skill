# Architectural Patterns

High-level patterns that define the overall structure and organization of a system.

---

## MVC / MVP / MVVM

**When to apply:**
- UI-heavy applications that need to separate presentation from business logic
- MVC: Web frameworks (Django, Spring MVC, Rails) — controller handles HTTP, model handles data, view renders
- MVP: Desktop/mobile UIs where the presenter mediates between view and model
- MVVM: Data-binding UIs (Android, WPF, React+Redux) — ViewModel exposes observable state

**Code smell signals:**
- Business logic mixed into view/template code
- Controllers/presenters doing database queries directly
- Views that are hard to test because they contain logic

**When NOT to apply:**
- Simple scripts or CLIs — MVC adds ceremony with no benefit
- Microservices that only expose APIs with no UI

---

## Hexagonal Architecture (Ports & Adapters)

**When to apply:**
- You want the core domain to be completely independent of infrastructure (databases, HTTP, queues)
- You need to test domain logic without any external dependencies
- You anticipate swapping infrastructure (e.g., switching databases, transport protocols)

**Core concept:**
- **Domain core** has zero dependencies on frameworks, ORMs, or transport
- **Ports** are interfaces the core exposes (inbound) or requires (outbound)
- **Adapters** are the concrete implementations (HTTP handlers, DB repositories, message consumers)

**Code smell signals:**
- `import "database/sql"` or `import "net/http"` directly inside domain/business logic packages
- Domain tests requiring a running database or HTTP server
- Business logic that changes whenever the database schema changes

**When NOT to apply:**
- Simple CRUD applications — Hexagonal adds significant structural overhead
- Small services where the domain and infrastructure are tightly bound by nature

**Language considerations:**
- Go: Packages as layers; interfaces in the domain package; concrete adapters in `internal/adapters/`
- Java: Clean package structure with modules; popular with Spring Boot (service layer isolates domain)
- Python: Domain layer as pure Python with no framework imports; adapters in separate packages
- Rust: Trait-based ports; workspace crates for clean separation

---

## Clean Architecture

**When to apply:**
- Similar goals to Hexagonal: keep business rules independent of frameworks and infrastructure
- You want strict dependency rules: outer layers depend on inner layers, never the reverse
- Long-lived systems where the domain model must survive technology changes

**Layers (inner to outer):**
1. Entities — enterprise-wide business rules
2. Use Cases — application-specific business rules
3. Interface Adapters — controllers, presenters, gateways
4. Frameworks & Drivers — UI, databases, external agencies

**Code smell signals:**
- Use case logic importing HTTP framework types
- Entity objects with ORM annotations mixed into domain logic
- Changes to the database schema forcing changes to business rules

**When NOT to apply:**
- Projects that won't outlive their framework choice
- Small teams where the discipline required outweighs the benefit

---

## Layered Architecture (N-Tier)

**When to apply:**
- Traditional enterprise applications: Presentation → Business Logic → Data Access
- Well-understood pattern that most teams know
- Monolithic applications where strict layer isolation is less critical

**Code smell signals:**
- Presentation layer calling data access directly (skipping business logic)
- Data access logic mixed into controllers
- No clear separation between what's UI vs. business vs. data

**When NOT to apply:**
- Performance-critical paths where layer traversal adds overhead
- Modern systems moving to hexagonal or clean architecture for better testability

---

## Microservices

**When to apply:**
- Independent deployability is required for different parts of the system
- Different services have different scaling requirements
- Separate teams own separate services and need deployment autonomy
- Business capabilities map cleanly to service boundaries

**Code smell signals (in a monolith that should be split):**
- Different modules have wildly different deployment frequencies
- A single change requires coordinating across many teams
- Independent scaling is blocked by a monolith's single process

**When NOT to apply:**
- Small teams — microservices operational overhead (networking, service discovery, distributed tracing) often outweighs benefits
- The domain boundaries are not well understood — "distributed monolith" is worse than a monolith
- Start with a monolith; extract services when you feel the pain

**Language considerations:**
- All languages: gRPC, REST, or message queues for inter-service communication
- Service mesh (Istio, Linkerd) for cross-cutting concerns at scale

---

## Event-Driven Architecture

**When to apply:**
- Components are naturally decoupled and communicate through events
- You need high scalability and loose coupling between producers and consumers
- Audit trails, activity feeds, or real-time reaction to state changes

**Code smell signals:**
- Services calling other services synchronously in a chain, creating cascading latency
- Tight coupling where Service A must know about Service B, C, D
- Long-running synchronous workflows that block the caller

**When NOT to apply:**
- Consistency requirements are strict — eventual consistency is unacceptable
- Small systems where the event infrastructure (brokers, serialization, dead-letter queues) adds more complexity than it saves
- Debugging is already hard — event-driven systems are harder to trace

**Language considerations:**
- Infrastructure: Kafka, RabbitMQ, NATS, AWS SNS/SQS
- All languages have clients; Go and Rust clients tend to be most performant

---

## Pipe and Filter

**When to apply:**
- Data processing pipelines where each step transforms or filters data independently
- Steps need to be composable, reorderable, or replaceable
- Classic use cases: ETL pipelines, compiler phases, image processing, log processing

**Code smell signals:**
- Long sequential processing functions where each stage directly calls the next
- Data transformation logic that can't be tested in isolation

**When NOT to apply:**
- Steps are tightly interdependent (output of one step affects the structure of the pipeline)
- Performance-critical paths where function call overhead matters

**Language considerations:**
- Go: `io.Reader`/`io.Writer` chains; channel pipelines
- Python: Generator pipelines (`yield`); `itertools` chains
- Java: `Stream` API; reactive streams (Reactor, RxJava)
- Rust: Iterator adapters (`map`, `filter`, `flat_map`) — zero-cost abstractions
