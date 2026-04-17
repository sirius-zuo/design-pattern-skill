# design-pattern-review

A Claude Code skill that reviews your project's architecture for design pattern applicability. It works in two modes:

- **Design Doc Review** — validates a design document or architecture spec against known patterns before you start coding
- **Code Review** — analyzes an existing codebase for pattern improvement opportunities

Covers 35+ patterns across GoF Creational, Structural, Behavioral, Modern (Repository, CQRS, Circuit Breaker, etc.), and Architectural (Hexagonal, Clean Architecture, Microservices, etc.) categories, with language-specific guidance for Java, Go, Python, and Rust.

## Installation

### Prerequisites

- [Claude Code](https://claude.ai/code) installed and configured

### Install the skill

```bash
# Clone the repo
git clone git@github.com:olivezuo/design-pattern-skill.git

# Copy the skill to your Claude Code skills directory
cp -r design-pattern-skill/design-pattern-review ~/.claude/skills/design-pattern-review

# Create the skill metadata file
cat > ~/.claude/skills/design-pattern-review/.skill-meta.json << 'EOF'
{
  "sourceType": "local",
  "source": "local",
  "skillName": "design-pattern-review",
  "path": "~/.claude/skills/design-pattern-review",
  "scope": "global"
}
EOF
```

The skill is immediately available in any Claude Code session — no restart required.

## Usage

Run the skill from within Claude Code:

```
/design-pattern-review
```

### Auto-detection

By default, the skill detects the appropriate mode automatically:

- If design/architecture documents are found (`docs/**/*.md`, `ARCHITECTURE.md`, `DESIGN.md`, etc.) → **Design Doc Review Mode**
- If no design docs are found → **Code Review Mode**

### Flags

| Flag | Description |
|------|-------------|
| `--design` | Force design document review mode |
| `--code` | Force code review mode |
| `--scope <path>` | Limit the review to a specific directory |

### Examples

```
# Auto-detect mode (recommended)
/design-pattern-review

# Review only the design documents
/design-pattern-review --design

# Review only the codebase
/design-pattern-review --code

# Review a specific module
/design-pattern-review --scope src/auth/

# Review both (run twice with explicit flags)
/design-pattern-review --design
/design-pattern-review --code
```

## Output

The skill produces a structured markdown report with:

- **Project Summary** — detected language(s), review mode, scope
- **Patterns Currently in Use** — identified patterns with an assessment (well implemented, misapplied, partially applied)
- **Recommended Patterns** — opportunities with location, impact, and priority
- **Detailed Recommendations** — for each opportunity: the problem, why the pattern fits, how to apply it, and trade-offs
- **Anti-Patterns Observed** — misapplied patterns with recommended alternatives

## Pattern Coverage

| Category | Patterns |
|----------|----------|
| Creational (GoF) | Abstract Factory, Builder, Factory Method, Prototype, Singleton |
| Structural (GoF) | Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy |
| Behavioral (GoF) | Chain of Responsibility, Command, Interpreter, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor |
| Modern | Repository, Dependency Injection, Circuit Breaker, Event Sourcing, CQRS, Saga, Retry/Backoff, Pub/Sub |
| Architectural | MVC/MVP/MVVM, Hexagonal/Ports & Adapters, Clean Architecture, Layered Architecture, Microservices, Event-Driven Architecture, Pipe & Filter |

## Repository Structure

```
design-pattern-review/
  SKILL.md                # Main skill definition
  patterns/
    creational.md         # Creational pattern detection rules
    structural.md         # Structural pattern detection rules
    behavioral.md         # Behavioral pattern detection rules
    modern.md             # Modern pattern detection rules
    architectural.md      # Architectural pattern detection rules
  report-template.md      # Report output template
docs/
  superpowers/specs/
    2026-04-17-design-pattern-review-skill-design.md  # Design spec
```

## Scope

This skill **recommends** — it does not refactor code. Each recommendation includes trade-offs so you can make an informed decision. It is a first-pass automated check and is not a substitute for human architectural review.
