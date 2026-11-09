<!--
Generic RFC starting template. Copy into the project's RFC directory (default
docs/rfcs/rfc-<n>-<slug>.md — check for an existing convention first, see SKILL.md §0),
rename the H1, and fill in each section. Delete this comment block once copied.
-->

# RFC-<n>: <Title>

| Author | Title | Category | Status | Dependencies | Date |
|---|---|---|---|---|---|
| <name> | <title> | <area/category> | draft | <other RFCs this depends on, or "none"> | <YYYY-MM-DD> |

| Revision | Date | Summary |
|---|---|---|
| 1 | <YYYY-MM-DD> | Initial draft |

**Summary**

<The problem, in a sentence or two, and the proposed change, in a sentence or two.>

**Rationale & Design Goals**

<Why this change is needed now — what triggered it (an incident, a measured gap, a new
requirement). Then the design goals/constraints, in priority order if they trade off:>

1. **<Goal name>.** <What it means concretely, and how you'll know it's satisfied.>
2. **<Goal name>.** <...>

<State what's explicitly out of scope, if anything could be mistaken for in-scope.>

---

## Specification

Requirement words (`MUST`, `SHOULD`, `MAY`) follow RFC 2119.

### 1. Definitions

| Term | Meaning |
|---|---|
| **<Term>** | <Precise definition — no forward references to undefined terms.> |

### 2. <Interface / format change>

<Exact shape: fields, types, encoding, defaults, limits.>

### 3. <Core behavior / algorithm>

<Normative behavior. Prefer numbered steps or pseudocode over prose when order matters.>

### 4. Validation & failure behavior

<What's rejected, what falls back, what the fail-safe default is.>

### 5. Test vectors

<Concrete input → expected output pairs, especially for edge cases and invalid input.>

## Security Considerations

<New attack surfaces this introduces, trust assumptions it relies on, and how it interacts
with existing invariants. If there is genuinely no security impact, state that explicitly
and say why — don't just omit the section.>

## Alternatives Considered (informative)

<What else was considered and why it was rejected. Not normative — doesn't bind the spec.>
