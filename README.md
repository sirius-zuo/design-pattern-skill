<p align="center">
  <img src="assets/Design-Pattern-Skill-Banner.jpg" alt="Design-Pattern-Skill" width="1280">
</p>



# Design-Pattern-SKILL

A Claude Code skill that reviews your project's architecture for design pattern applicability. It works in two modes:

- **Design Doc Review** — validates a design document or architecture spec against known patterns before you start coding
- **Code Review** — analyzes an existing codebase for pattern improvement opportunities

Covers 35+ patterns across GoF Creational, Structural, Behavioral, Modern (Repository, CQRS, Circuit Breaker, etc.), and Architectural (Hexagonal, Clean Architecture, Microservices, etc.) categories, with language-specific guidance for Java, Go, Python, and Rust.

## Installation

The skill is built as a Claude Code native skill, but the pattern knowledge files can be wired into any AI coding agent that supports custom instructions or context files. Clone the repo first, then follow the instructions for your agent.

```bash
git clone git@github.com:sirius-zuo/design-pattern-skill.git
```

---

### Claude Code

**Prerequisites:** [Claude Code](https://claude.ai/code) installed and configured.

```bash
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

The skill is immediately active. Invoke it with `/design-pattern-review`.

---

### Cursor

Add a rule file that instructs Cursor to use the pattern knowledge when reviewing architecture.

Create `.cursor/rules/design-pattern-review.mdc` in your project root:

```markdown
---
description: Review architecture and code for design pattern opportunities
globs:
alwaysApply: false
---

When asked to review architecture or suggest design patterns, follow the process in:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/SKILL.md

Use the pattern knowledge from:
- <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/creational.md
- <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/structural.md
- <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/behavioral.md
- <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/modern.md
- <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/architectural.md

Output the report using the template in:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/report-template.md
```

Replace `<path-to-cloned-repo>` with the absolute path where you cloned this repo.

Then trigger it in Cursor chat: `@design-pattern-review.mdc Review this codebase for design pattern opportunities.`

---

### GitHub Copilot

Add instructions to `.github/copilot-instructions.md` in your project root (create it if it doesn't exist):

```markdown
## Design Pattern Review

When asked to review architecture or suggest design patterns, read and follow the instructions at:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/SKILL.md

Reference the pattern knowledge files in:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/

Output findings using the report template at:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/report-template.md
```

Then ask Copilot in chat: `Review this project for design pattern opportunities following the design-pattern-review instructions.`

---

### Windsurf

Create `.windsurfrules` in your project root (or append to it if it exists):

```markdown
## Design Pattern Review

When asked to review architecture or suggest design patterns, follow the process defined in:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/SKILL.md

Use the pattern reference files in:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/

Format output using:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/report-template.md
```

Then ask in Cascade chat: `Review this codebase for design pattern opportunities.`

---

### OpenAI Codex CLI

Add instructions to `AGENTS.md` in your project root (create it if it doesn't exist):

```markdown
## Design Pattern Review

When asked to review architecture or suggest design patterns, read and follow:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/SKILL.md

Reference the pattern files in:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/

Format the output report using:
<path-to-cloned-repo>/design-pattern-skill/design-pattern-review/report-template.md
```

Then run: `codex "Review this project for design pattern opportunities"`

---

### Aider

Pass the skill files as context when starting an aider session:

```bash
aider \
  --read <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/SKILL.md \
  --read <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/creational.md \
  --read <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/structural.md \
  --read <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/behavioral.md \
  --read <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/modern.md \
  --read <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/patterns/architectural.md \
  --read <path-to-cloned-repo>/design-pattern-skill/design-pattern-review/report-template.md
```

Then in the aider session: `/ask Review this codebase for design pattern opportunities following the SKILL.md instructions.`

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
```

## Scope

This skill **recommends** — it does not refactor code. Each recommendation includes trade-offs so you can make an informed decision. It is a first-pass automated check and is not a substitute for human architectural review.
