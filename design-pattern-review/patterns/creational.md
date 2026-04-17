# Creational Patterns

Patterns that deal with object creation mechanisms, decoupling creation from usage.

---

## Abstract Factory

**When to apply:**
- The system needs to create families of related objects without specifying concrete classes
- You need to swap entire product families (e.g., switching UI themes, database backends)
- Code creates multiple related objects that must be compatible with each other

**When NOT to apply:**
- Only one type of object is being created (use Factory Method instead)
- The family of objects never changes — over-engineered for a fixed set

**Language considerations:**
- Java/Go: Typically expressed as interfaces with multiple implementations
- Python: Duck typing makes this lighter — protocols or ABCs work well
- Rust: Traits with associated types fit naturally

---

## Builder

**When to apply:**
- Object construction involves many optional parameters or complex multi-step configuration
- The same construction process needs to produce different representations
- Code has constructors/functions with 4+ parameters, especially optional ones

**Code smell signals:**
- Constructor with many parameters (especially booleans/optionals)
- Multiple overloaded constructors or factory methods for different configurations
- Object that can only be fully configured after multiple method calls

**When NOT to apply:**
- Simple objects with 2-3 required fields — Builder adds unnecessary ceremony
- Immutable value objects with few fields (prefer a record/struct)

**Language considerations:**
- Java: Classic Builder pattern with inner static Builder class
- Go: Functional options pattern (`WithX(val) Option`) is idiomatic over classic Builder
- Python: `@dataclass` with keyword-only args often suffices; Builder is rarely needed
- Rust: The `builder` crate or method chaining returning `Self` is common

---

## Factory Method

**When to apply:**
- A class can't anticipate the type of objects it needs to create
- Subclasses need to control which class is instantiated
- Object creation logic is complex enough to warrant its own method

**Code smell signals:**
- `if/else` or `switch` on a type to determine which object to create
- Creation logic duplicated in multiple places
- Callers need to know concrete class names when they shouldn't

**When NOT to apply:**
- Creation is trivial — a simple constructor call is clearer
- Only one concrete type will ever be created

**Language considerations:**
- Go: Package-level `New*` functions serve as factory methods; no inheritance needed
- Python: Class methods (`@classmethod`) are the idiomatic factory method
- Rust: Associated functions (`Type::new()` or `Type::from_*()`) are conventional

---

## Prototype

**When to apply:**
- Object creation is expensive and a cloned copy is as useful as a new instance
- You need to create objects similar to existing ones at runtime
- Classes to instantiate are specified at runtime

**Code smell signals:**
- Manual deep-copy code scattered across the codebase
- Expensive initialization (DB queries, file parsing) repeated for similar objects

**When NOT to apply:**
- Objects are cheap to create — cloning adds complexity without benefit
- Deep copying complex graphs is error-prone and a simpler approach is available

**Language considerations:**
- Java: `Cloneable` interface (but prefer copy constructors); records have auto-copy
- Go: No built-in clone — be explicit about shallow vs. deep copy
- Python: `copy.copy()` / `copy.deepcopy()` built in
- Rust: `Clone` and `Copy` traits; `#[derive(Clone)]` for most cases

---

## Singleton

**When to apply:**
- Exactly one instance of a class must coordinate actions across the system
- Classic use cases: configuration, logging, registry, connection pool

**When NOT to apply:**
- Global state is hidden behind the Singleton — prefer Dependency Injection instead
- Testing is required: Singletons make unit testing very hard
- Multiple instances might actually be needed later (premature constraint)

**Code smell signals:**
- `static getInstance()` everywhere in the codebase
- Tests that are order-dependent or that modify global state

**Language considerations:**
- Java: Classic double-checked locking or enum Singleton; often replaced by DI frameworks
- Go: Package-level variables are effectively singletons — `sync.Once` for lazy init
- Python: Module-level objects are singletons naturally; `__new__` override if needed
- Rust: `lazy_static!` or `once_cell::sync::Lazy` for safe global singletons
