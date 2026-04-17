# Design Pattern Review Skill - Design Spec

## Context

When building software, design patterns provide proven solutions to common problems. However, developers often miss opportunities to apply patterns, misapply them, or don't validate their architecture designs against known patterns before implementation. This skill provides an automated first-pass review that identifies pattern opportunities and validates existing pattern usage -- both in design documents (pre-implementation) and existing codebases (post-implementation).

This spec covers the **review skill only**. The companion MCP template service is a separate project with its own spec/plan cycle.

## Overview

**Skill name:** `design-pattern-review`

A Claude Code skill that reviews a project's architecture for design pattern applicability. It operates in two auto-detected modes: reviewing design documents before implementation, or analyzing existing codebases for pattern improvement opportunities. Outputs a structured report with findings and recommendations.

**Install location:** `~/.claude/skills/design-pattern-review/` (local skill, separate repo)

**Trigger:** Use when reviewing a project's architecture for design pattern applicability -- either validating a design document before implementation, or analyzing an existing codebase for pattern improvement opportunities.

## Modes of Operation

### Auto-Detection Logic

1. Scan for design/architecture documents (files matching `docs/**/*.md`, `specs/**/*.md`, `*architecture*`, `*design*`, `ARCHITECTURE.md`, `DESIGN.md`)
2. If design docs found → **Design Doc Review Mode** (reviews the design documents; does not also scan code unless `--code` is passed)
3. If no design docs found → **Code Review Mode** (analyzes the codebase directly)
4. If both design docs and code exist and the user wants both reviewed, they run the skill twice with explicit flags (`--design` then `--code`)
5. User can always override with flags

### Manual Overrides

- `/design-pattern-review --design` -- force design doc review mode
- `/design-pattern-review --code` -- force code review mode
- `/design-pattern-review --scope src/auth/` -- limit review to a specific directory

### Design Doc Review Mode

Triggered when architecture/design documents are found.

**Process:**
1. Parse the design document for architectural decisions, component descriptions, data flow
2. Map described components to known design patterns
3. Identify areas where patterns are mentioned but misapplied
4. Suggest patterns for areas that would benefit (e.g., "your plugin system description suggests a Strategy or Plugin pattern")

### Code Review Mode

Triggered when no design docs are found, or when explicitly requested.

**Process:**
1. Detect project language(s) from config files (go.mod, package.json, pyproject.toml, Cargo.toml, pom.xml, etc.)
2. Use Explore agents to scan codebase structure (directories, key files, class hierarchies)
3. Identify code smells that indicate pattern opportunities:
   - Large conditional/switch blocks → Strategy, State, or Command
   - Duplicated object creation logic → Factory or Builder
   - Tight coupling between modules → Observer, Mediator, or Dependency Injection
   - God classes/files → Facade, decomposition with patterns
   - Complex object construction → Builder
   - Cross-cutting concerns → Decorator, Proxy
4. Check for patterns already in use and assess their correctness

## Report Structure

The skill outputs a structured markdown report:

```
## Design Pattern Review Report

### Project Summary
- Language(s): [detected]
- Mode: [Design Doc / Code Review]
- Scope: [full project / specific directory]

### Patterns Currently in Use
| Pattern | Location | Assessment |
|---------|----------|------------|
| Observer | src/events/ | Well implemented |
| Singleton | src/config.go | Anti-pattern in this context - consider DI |

### Recommended Patterns
| Opportunity | Suggested Pattern | Location | Impact | Priority |
|------------|-------------------|----------|--------|----------|
| Complex object creation | Builder | src/models/user.go | High | Medium |
| Switch on type | Strategy | src/handlers/ | Medium | High |

### Detailed Recommendations
[For each recommendation: what the problem is, which pattern addresses it,
why it's appropriate here, and a brief sketch of how to apply it]

### Summary
- Patterns in use: N
- New opportunities identified: M
- High priority recommendations: K
```

## Pattern Knowledge Base

The skill carries embedded pattern knowledge organized into five categories:

### Categories

1. **Creational Patterns** (GoF): Abstract Factory, Builder, Factory Method, Prototype, Singleton
2. **Structural Patterns** (GoF): Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy
3. **Behavioral Patterns** (GoF): Chain of Responsibility, Command, Interpreter, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor
4. **Modern Patterns**: Repository, Dependency Injection, Circuit Breaker, Event Sourcing, CQRS, Saga, Retry/Backoff, Pub/Sub
5. **Architectural Patterns**: MVC/MVP/MVVM, Hexagonal/Ports & Adapters, Clean Architecture, Layered Architecture, Microservices, Event-Driven Architecture, Pipe & Filter

### Per-Pattern Knowledge

For each pattern, the knowledge base includes:
- **When to apply** -- the code smells or design signals that suggest this pattern
- **When NOT to apply** -- anti-patterns and over-engineering warnings
- **Language considerations** -- patterns that are irrelevant or implemented differently per language (e.g., Singleton is trivial in Go with package-level vars, Iterator is built into Python)

## Skill File Structure

```
design-pattern-review/
  SKILL.md                    # Main skill definition with frontmatter
  patterns/
    creational.md             # Creational pattern detection rules
    structural.md             # Structural pattern detection rules
    behavioral.md             # Behavioral pattern detection rules
    modern.md                 # Modern pattern detection rules
    architectural.md          # Architectural pattern detection rules
  report-template.md          # Report output template
```

Pattern files are supporting reference docs loaded contextually -- only the ones relevant to the detected language and codebase characteristics are loaded, not all of them every time.

## Edge Cases

- **No recognizable language** -- Report that language couldn't be auto-detected, ask user to specify or proceed with language-agnostic review
- **Monorepo / multi-language project** -- Detect all languages present, review each module in its own context
- **Very large codebase** -- Use `--scope` to focus. Without it, sample key directories (entry points, models, handlers) rather than full scan
- **Design doc but no code yet** -- Pure design doc review, skip code analysis
- **No design docs and minimal code** -- Brief assessment with suggestions for patterns to consider as the project grows
- **Patterns already well-applied** -- Acknowledge good usage, don't force unnecessary recommendations

## Scope Boundaries

**What the skill does:**
- Reviews and recommends design patterns
- Explains trade-offs for each recommendation
- Identifies existing pattern usage and assesses correctness
- Adapts recommendations to the detected language

**What the skill does NOT do:**
- Refactor code -- it only recommends. Implementation is the developer's job
- Enforce patterns dogmatically -- it explains trade-offs and lets the developer decide
- Replace architectural review by humans -- it's a first-pass automated check

**Relationship to TOGAF skills:**
- design-pattern-review focuses on GoF/modern/architectural patterns at the code and module level
- TOGAF skills handle enterprise architecture at a much higher level (business architecture, technology portfolios, migration planning)
- They are complementary but independent

## Future Work (Out of Scope for This Spec)

- **MCP Design Pattern Template Service** -- A separate MCP server providing language-agnostic pseudocode templates plus idiomatic implementations in Java, Go, Python, and Rust. Will be designed and built as an independent project.
- Integration between the review skill and the MCP template service (querying templates during review)

## Verification

To test the skill after implementation:
1. Install the skill to `~/.claude/skills/design-pattern-review/`
2. Test Design Doc Mode: run against a project with architecture docs and verify the report correctly identifies described patterns and suggests improvements
3. Test Code Review Mode: run against an existing codebase with known pattern opportunities (e.g., a project with obvious Strategy or Factory candidates) and verify the report catches them
4. Test auto-detection: verify mode switches correctly based on presence/absence of design docs
5. Test `--scope` flag: verify the review is limited to the specified directory
6. Test edge cases: empty project, monorepo, no recognizable language
