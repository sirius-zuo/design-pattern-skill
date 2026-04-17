---
name: design-pattern-review
description: Use when reviewing a project's architecture for design pattern applicability -- either validating a design document before implementation, or analyzing an existing codebase for pattern improvement opportunities.
---

# Design Pattern Review

Reviews a project's architecture against known design patterns and outputs a structured report with findings and recommendations.

## When to Use

- You have a design document or architecture spec and want to validate it uses appropriate patterns before coding
- You have an existing codebase and want to identify where patterns could improve structure, maintainability, or extensibility
- You want a systematic first-pass audit of pattern usage before a major refactor

## Flags

- `--design` — force design document review mode
- `--code` — force code review mode
- `--scope <path>` — limit review to a specific directory (e.g., `--scope src/auth/`)

## Process

### Step 1: Auto-Detect Mode

Unless overridden by a flag, detect mode automatically:

1. Scan for design/architecture documents: `docs/**/*.md`, `specs/**/*.md`, `ARCHITECTURE.md`, `DESIGN.md`, files with "architecture" or "design" in the name
2. If design docs found → **Design Doc Review Mode**
3. If no design docs found → **Code Review Mode**

If both design docs and code exist and the user wants both reviewed, run the skill twice: once with `--design`, once with `--code`.

### Step 2: Context Discovery

Before reviewing, gather context:

1. Detect project language(s) from config files:
   - Go: `go.mod`
   - Node/JS/TS: `package.json`
   - Python: `pyproject.toml`, `setup.py`, `requirements.txt`
   - Rust: `Cargo.toml`
   - Java: `pom.xml`, `build.gradle`
2. Note the directory scope (full project or `--scope` path)
3. Load relevant pattern knowledge from `patterns/` based on the project type

### Step 3A: Design Doc Review Mode

1. Read the identified design documents
2. Map described components and interactions to known patterns:
   - Look for explicit pattern mentions and assess if they are correctly applied
   - Look for described behaviors that match pattern signatures (e.g., "plugins that can be swapped" → Strategy; "notifications to multiple subscribers" → Observer)
3. For each unmapped area of the design, consider which patterns could strengthen it
4. Note any anti-patterns or misapplied patterns

**Load relevant pattern files:**
- Check `patterns/creational.md` for object creation concerns
- Check `patterns/structural.md` for component composition and relationships
- Check `patterns/behavioral.md` for interactions and responsibilities
- Check `patterns/modern.md` for data access, resilience, and messaging concerns
- Check `patterns/architectural.md` for overall structure and layering concerns

### Step 3B: Code Review Mode

1. Use Explore agents to scan codebase structure (key directories, entry points, models, handlers)
   - For large codebases without `--scope`: sample entry points, models, handlers, services — do not attempt a full scan
2. Identify code smells that signal pattern opportunities:
   - Large conditional/switch blocks → Strategy, State, or Command
   - Duplicated object creation logic → Factory or Builder
   - Tight coupling between modules → Observer, Mediator, or Dependency Injection
   - God classes or oversized files → Facade, decomposition with patterns
   - Complex multi-step object construction → Builder
   - Cross-cutting concerns (logging, auth, caching) tangled in business logic → Decorator, Proxy
   - Direct database queries scattered across the codebase → Repository
3. Check for patterns already in use and assess correctness
4. Adapt recommendations to the detected language (see language considerations in each pattern file)

**Load relevant pattern files:**
- Check `patterns/creational.md` for object creation concerns
- Check `patterns/structural.md` for composition and coupling concerns
- Check `patterns/behavioral.md` for interaction and responsibility concerns
- Check `patterns/modern.md` for data access, resilience, messaging
- Check `patterns/architectural.md` for overall layering and structural concerns

### Step 4: Generate Report

Use the template in `report-template.md` to produce a structured markdown report.

Save the report to `./docs/review/YYYY-MM-DD-<project-name>-pattern-review.md` where:
- `YYYY-MM-DD` is today's date
- `<project-name>` is the project or repository name derived from the root directory name or `package.json`/`go.mod`/`Cargo.toml`

Example: `./docs/review/2026-04-17-my-service-pattern-review.md`

Create the `docs/review/` directory if it does not exist. Also output the report inline so the user can read it immediately without opening the file.

## Edge Cases

- **No recognizable language** — Proceed with language-agnostic review; note that language-specific considerations are unavailable
- **Monorepo / multi-language project** — Detect all languages; review each module in its own context
- **Very large codebase without `--scope`** — Sample key directories only; suggest the user re-run with `--scope` for deeper analysis of specific areas
- **Design doc but no code** — Pure design doc review; skip code analysis
- **No design docs and minimal code** — Brief assessment with pattern suggestions for as the project grows
- **Patterns already well-applied** — Acknowledge good usage; do not force unnecessary recommendations

## Scope Boundaries

This skill **does not**:
- Refactor code — it only recommends
- Enforce patterns dogmatically — it explains trade-offs
- Replace human architectural review — it is a first-pass automated check
