# Design Pattern Review Report

## Project Summary

| Field | Value |
|-------|-------|
| Language(s) | [detected languages] |
| Mode | [Design Doc Review / Code Review] |
| Scope | [full project / `<path>`] |
| Design docs reviewed | [list of files, or "none"] |

---

## Patterns Currently in Use

> Patterns identified in the [design document / codebase] and an assessment of their usage.

| Pattern | Category | Location | Assessment |
|---------|----------|----------|------------|
| [Pattern name] | [Creational/Structural/Behavioral/Modern/Architectural] | [File or module] | [Well implemented / Misapplied: reason / Partially applied: what's missing] |

_If no patterns were identified: "No explicit design patterns detected."_

---

## Recommended Patterns

> Opportunities where applying a pattern would improve structure, maintainability, or extensibility.

| Opportunity | Suggested Pattern | Location | Impact | Priority |
|------------|-------------------|----------|--------|----------|
| [Description of the code smell or design gap] | [Pattern name] | [File, module, or design section] | [High / Medium / Low] | [High / Medium / Low] |

_Priority: How urgently the change is recommended. Impact: How much it would improve the codebase._

_If no new patterns are recommended: "Existing structure is well-suited for the current design. No new patterns recommended at this time."_

---

## Detailed Recommendations

> For each recommended pattern above, a deeper explanation.

### [Pattern Name] — [Location]

**Problem:** [Describe the code smell, design gap, or inefficiency observed.]

**Why this pattern fits:** [Explain why this specific pattern addresses the problem. Reference language considerations where relevant.]

**How to apply:** [Brief sketch of the approach — not a full implementation, but enough to guide the developer. For example: "Extract the switch statement in `handlers/process.go` into a `Processor` interface with concrete types for each case."]

**Trade-offs to consider:** [Any reasons the developer might choose not to apply this pattern, or caveats about the approach.]

---

## Anti-Patterns Observed

> Patterns that are in use but are causing problems or being misapplied.

| Anti-Pattern | Location | Issue | Recommended Alternative |
|-------------|----------|-------|------------------------|
| [Pattern name or anti-pattern] | [Location] | [Why it's problematic here] | [Better approach] |

_If none: "No anti-patterns observed."_

---

## Summary

- **Patterns in use:** [N]
- **New opportunities identified:** [M]
- **High priority recommendations:** [K]
- **Anti-patterns observed:** [J]

> [Optional 2-3 sentence overall assessment of the architecture's pattern maturity and the most impactful next step.]
