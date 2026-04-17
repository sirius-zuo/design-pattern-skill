# Behavioral Patterns

Patterns concerned with algorithms and the assignment of responsibilities between objects.

---

## Chain of Responsibility

**When to apply:**
- More than one object may handle a request, and the handler isn't known a priori
- You want to issue a request without specifying the receiver explicitly
- The set of handlers and their order should be configurable

**Code smell signals:**
- Nested `if/else` chains checking conditions to decide which handler to use
- Middleware pipelines where each step may terminate or pass along the request

**When NOT to apply:**
- The request always has exactly one handler — just call it directly
- Chain is so long it becomes hard to trace request flow

**Language considerations:**
- Go: HTTP middleware (`func(http.Handler) http.Handler`) is idiomatic CoR
- Python: Lists of handler callables; middleware in Django/Flask
- Java: Servlet filters, Spring interceptors

---

## Command

**When to apply:**
- You need to parameterize actions, queue them, log them, or support undo/redo
- You need to implement transactional behavior or macro recording
- Callbacks need to be stored, passed, or invoked at a later time

**Code smell signals:**
- Undo/redo logic scattered in multiple places
- Action history or audit log built ad hoc
- Long methods deciding which operation to perform based on a parameter

**When NOT to apply:**
- Simple callbacks suffice — full Command objects add overhead
- Undo is not needed and operations are fire-and-forget

**Language considerations:**
- Go: Functions are first-class; simple commands are just `func()` closures
- Python: Callables or `__call__` objects; `functools.partial` for parameterized commands
- Java: Functional interfaces (`Runnable`, `Callable`) or explicit Command classes
- Rust: Closures (`Box<dyn Fn()>`) or trait objects

---

## Interpreter

**When to apply:**
- You need to interpret sentences in a simple language (configuration DSLs, query languages, expression evaluators)
- The grammar is simple and efficiency is not the primary concern

**When NOT to apply:**
- Grammar is complex — use a proper parser generator instead
- Performance is critical — interpreted trees are slow

**Language considerations:**
- All languages: Recursive descent parser or visitor over an AST; consider libraries like ANTLR (Java), `pest` (Rust), `lark` (Python)

---

## Iterator

**When to apply:**
- You need to traverse elements of a collection without exposing its internal structure
- You need multiple simultaneous traversals of the same collection

**When NOT to apply:**
- Language provides built-in iteration — use it directly

**Language considerations:**
- Python: `__iter__` / `__next__` / generators — iteration is deeply built in
- Go: `range` covers most cases; custom iterators via `func() (T, bool)`; Go 1.23+ `iter.Seq`
- Java: `Iterable<T>` / `Iterator<T>` / enhanced for loop / Stream API
- Rust: `Iterator` trait — highly composable with adapters

---

## Mediator

**When to apply:**
- Many objects communicate in complex ways, leading to tight coupling
- You want to centralize communication between objects (chat room, air traffic control, event bus)
- Removing direct references between objects to simplify dependencies

**Code smell signals:**
- Objects holding references to many other objects just to notify them
- Changes to one class requiring changes in many others

**When NOT to apply:**
- Only two objects communicate — simple direct reference is clearer
- Mediator becomes a "god object" doing too much — decompose it

**Language considerations:**
- All languages: Event bus, message broker, or coordinator service
- Go: Channels are natural mediators between goroutines

---

## Memento

**When to apply:**
- You need to save and restore an object's state (undo/redo, snapshots, checkpoints)
- You want to snapshot state without exposing internal representation

**When NOT to apply:**
- Serialization/deserialization (JSON, protobuf) already handles state persistence
- Object state is trivial to recreate

**Language considerations:**
- Python: `copy.deepcopy()` is often sufficient for simple mementos
- Rust: `Clone` for value types; more explicit for complex graphs

---

## Observer

**When to apply:**
- A change in one object requires changing others, and you don't know how many objects need to change
- Objects should be able to notify other objects without making assumptions about who those objects are
- Event-driven systems: UI updates, event handlers, reactive streams

**Code smell signals:**
- Direct method calls to notify many listeners hardcoded in one class
- Polling loops checking for state changes instead of being notified

**When NOT to apply:**
- The relationship between subject and observer is fixed and there's only one observer
- Event chains become hard to trace (unexpected cascading updates)

**Language considerations:**
- Go: Channels for goroutine notification; callback slices for synchronous observers
- Python: `Observable` or event libraries; `asyncio` events for async
- Java: `java.util.Observable` (deprecated) → use `PropertyChangeSupport` or reactive streams
- Rust: `tokio::sync::watch` or `broadcast` channels; callback `Vec<Box<dyn Fn()>>`

---

## State

**When to apply:**
- An object changes its behavior based on its internal state
- State-specific behavior is duplicated across many conditionals
- You have a state machine with multiple transitions

**Code smell signals:**
- Large `if/else` or `switch` on a state enum field spread across many methods
- State transition logic mixed with business logic

**When NOT to apply:**
- Only two states with minimal behavior — a simple boolean suffices
- States don't have meaningfully different behavior

**Language considerations:**
- Rust: Enums with methods are a natural state machine
- Go: Interface per state, swap the implementation pointer
- Python/Java: State interface with concrete state classes

---

## Strategy

**When to apply:**
- You need to define a family of algorithms, encapsulate each one, and make them interchangeable
- You want to eliminate conditionals that choose between algorithms
- Algorithm selection needs to vary independently from the code that uses it

**Code smell signals:**
- Large `if/else` or `switch` selecting an algorithm based on a type/flag
- Duplicate method bodies that differ only in one algorithm step
- Subclasses created solely to override one method with a different algorithm

**When NOT to apply:**
- Only one algorithm will ever be used — abstraction is unnecessary
- The context always knows which algorithm to use at compile time (just call it directly)

**Language considerations:**
- Go: Interfaces with a single method; functions as strategies
- Python: Callable objects or plain functions; `functools` for composition
- Java: Functional interfaces make single-method strategies concise
- Rust: Trait objects (`Box<dyn Strategy>`) or generic type parameters with trait bounds

---

## Template Method

**When to apply:**
- Multiple classes share the same algorithm skeleton but differ in specific steps
- You want to control which parts of an algorithm subclasses can override

**Code smell signals:**
- Copied method bodies across subclasses with only a few lines different
- `if isinstance(self, SubclassA)` checks inside a base class method

**When NOT to apply:**
- Composition (Strategy) is more flexible — prefer it when the varying step needs to change at runtime
- Deep inheritance hierarchies make Template Method hard to trace

**Language considerations:**
- Go: No inheritance — use Strategy with function injection instead
- Python: ABC with abstract methods works; hook methods with default no-op implementations
- Java: Abstract class with abstract methods for the variable steps

---

## Visitor

**When to apply:**
- You need to perform many distinct and unrelated operations on an object structure without polluting those classes
- The object structure rarely changes but you often define new operations on it
- Double dispatch is needed (action depends on both the operation type and the element type)

**Code smell signals:**
- Methods on element classes that don't belong to the element's core responsibility
- Type-checking (`instanceof`/type switch) to dispatch different operations

**When NOT to apply:**
- The object structure changes frequently — adding a new element type requires updating all visitors
- Only one operation is needed — just add a method to the class

**Language considerations:**
- Rust: Enums with pattern matching are often cleaner than Visitor
- Go: Type switches serve a similar purpose for simple cases; full Visitor for complex hierarchies
- Java/Python: Classic accept/visit double-dispatch pattern
