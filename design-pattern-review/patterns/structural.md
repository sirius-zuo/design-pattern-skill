# Structural Patterns

Patterns that deal with object composition, creating relationships between objects to form larger structures.

---

## Adapter

**When to apply:**
- You want to use an existing class but its interface doesn't match what you need
- You're integrating a third-party library or legacy component with an incompatible interface
- Multiple components with different interfaces need to work together

**Code smell signals:**
- Conversion/translation code duplicated at every call site
- Wrapper functions sprinkled throughout the codebase to normalize a third-party API

**When NOT to apply:**
- The interface mismatch is trivial (a rename) — just rename it
- You control both sides and can align the interfaces directly

**Language considerations:**
- Go: Interface satisfaction is implicit — adapters are natural and lightweight
- Python: Duck typing reduces the need; use when explicit interface compliance is needed
- Rust: Implement the target trait for the adaptee type

---

## Bridge

**When to apply:**
- You want to separate an abstraction from its implementation so both can vary independently
- You have a class hierarchy that's growing in two independent dimensions
- You want to switch implementations at runtime

**Code smell signals:**
- Class explosion: `RedCircle`, `BlueCircle`, `RedSquare`, `BlueSquare` — two dimensions multiplying
- Subclasses created only to change a small implementation detail

**When NOT to apply:**
- Only one dimension varies — Bridge adds unnecessary indirection
- The abstraction and implementation are unlikely to change independently

**Language considerations:**
- Java: Classic bridge with abstract class + Implementor interface
- Go: Composition with interfaces is the idiomatic bridge
- Python: Composition over inheritance; inject the implementation

---

## Composite

**When to apply:**
- You need to treat individual objects and compositions of objects uniformly
- You're building tree structures: file systems, UI component trees, organizational charts, expression trees

**Code smell signals:**
- Code that checks `if leaf else if branch` to handle parts of a tree differently
- Recursive traversal logic duplicated across the codebase

**When NOT to apply:**
- The hierarchy is flat and won't grow — over-engineered for a simple list
- Type safety is critical and uniform treatment hides important differences

**Language considerations:**
- Go: Interfaces with `Add(Component)` and `Remove(Component)` methods
- Python: `__iter__` and duck typing make composites natural
- Rust: Enums with recursive variants (e.g., `Node::Leaf(val)` / `Node::Branch(Vec<Node>)`)

---

## Decorator

**When to apply:**
- You need to add responsibilities to objects dynamically without subclassing
- You want to combine behaviors in flexible ways at runtime
- Cross-cutting concerns (logging, caching, auth, retries) need to wrap core behavior

**Code smell signals:**
- Subclass explosion to add combinations of features
- Feature flags in the base class that enable/disable behaviors
- Middleware chains that wrap a core handler

**When NOT to apply:**
- The behavior is always the same — just put it in the class directly
- Decorator stacks become hard to debug — consider a simpler composition approach

**Language considerations:**
- Python: `@decorator` syntax makes this native; `functools.wraps` for function decorators
- Go: Function wrapping (`http.Handler` middleware) is the idiomatic approach
- Java: Classic Decorator with interface wrapping (also: `java.io` streams)
- Rust: Newtype pattern or trait implementations for type-safe decoration

---

## Facade

**When to apply:**
- A complex subsystem needs a simplified interface for common use cases
- You want to decouple clients from a complex or legacy subsystem
- Multiple clients repeat the same sequence of subsystem calls

**Code smell signals:**
- Business logic orchestrating many subsystem objects scattered across callers
- Test setup requiring many dependencies to test simple behavior

**When NOT to apply:**
- The subsystem is already simple — Facade adds a useless layer
- Clients legitimately need access to subsystem details — Facade would hide necessary functionality

**Language considerations:**
- All languages: A service class or module that coordinates subsystem components

---

## Flyweight

**When to apply:**
- You need to support a very large number of fine-grained objects efficiently
- Most object state can be made extrinsic (passed in) rather than stored per-instance

**Code smell signals:**
- Memory pressure from thousands of similar objects with mostly shared data
- Objects in a tight rendering loop or physics simulation

**When NOT to apply:**
- Object count is small — premature optimization
- The extrinsic state management adds more complexity than it saves

**Language considerations:**
- Java: String interning is a classic Flyweight
- Go: Sync.Pool for object reuse; struct embedding for shared state
- Rust: `Arc<T>` for shared immutable state; `Rc<T>` in single-threaded contexts

---

## Proxy

**When to apply:**
- You need to control access to an object (access control, lazy init, logging, caching, remote access)
- Virtual proxy: defer expensive object creation until actually needed
- Protection proxy: check permissions before forwarding calls
- Caching proxy: cache results of expensive operations

**Code smell signals:**
- Access control or caching logic mixed into the real subject
- Lazy initialization checks scattered throughout the codebase

**When NOT to apply:**
- The overhead of proxying outweighs the benefit
- Aspect-oriented tools (Spring AOP, Python decorators) can handle it more cleanly

**Language considerations:**
- Python: `__getattr__` proxy pattern; also `functools.cached_property` for virtual proxy
- Go: Interface wrapping — proxy implements the same interface as the real subject
- Java: `java.lang.reflect.Proxy` for dynamic proxies; Spring AOP
- Rust: Smart pointers (`Box`, `Rc`, `Arc`) are proxies; custom Deref implementations
