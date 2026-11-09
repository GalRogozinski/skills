---
name: rfc
description: Use when drafting, reviewing, or editing an RFC / design proposal / technical spec for a software project — starting a new numbered RFC, checking one is precise enough for independent implementations, or following its review lifecycle. Works in any repo; detects and mirrors that repo's existing RFC conventions (prefix, directory, template, status vocabulary) if one already exists, otherwise applies sensible defaults.
---

# RFC Authoring

Generic guidance for writing RFCs (design/spec proposals) in any codebase. Adapted from
SSV Labs' SIP process, generalized so it isn't tied to one project's domain (blockchain
consensus, slashing, etc.) or one project's file layout.

## 0. Detect this project's conventions first — don't impose defaults over an existing process

Before drafting, check whether this repo already has an RFC-like process:

- An existing RFC/ADR directory: `docs/rfcs/`, `rfcs/`, `docs/adr/`, `docs/design/`, etc.
- An existing template file (`template_*.md`, `0000-template.md`, ADR template).
- A numbering prefix already in use (e.g. `MRFC-1`, `ADR-001`, `RFC-0042`) — grep existing
  docs for it.
- Mentions of the process in `CLAUDE.md`, `CONTRIBUTING.md`, or a top-level `docs/` index.

If any of these exist, **mirror them exactly**: same directory, same prefix, same metadata
table columns, same section names/order, same status vocabulary. Read one or two existing
RFCs in the repo first — they're the ground truth, not this file. Only fall back to the
defaults below when the repo has no established convention, and say so when you create the
first one (you're setting precedent, not just filling in a template).

## 1. Default location & naming (when nothing exists yet)

- Directory: `docs/rfcs/`
- Filename: `rfc-<n>-<slug>.md` (or the project's natural short code instead of `rfc`, e.g. a
  project called "mrkts" might use `mrfc-<n>-<slug>.md` — ask or infer, don't guess silently
  if it's ambiguous).
- **Assign the number when the RFC is ready to merge**, not at draft creation — parallel
  drafts would otherwise collide on the same number.

## 2. Required sections

| Section | Purpose |
|---|---|
| Metadata table | `Author \| Title \| Category \| Status \| Dependencies \| Date` |
| Revision history (optional, recommended once an RFC gets revised) | `Revision \| Date \| Summary` — one row per substantive revision pass |
| **Summary** | The problem and the proposed change, in a few sentences |
| **Rationale & Design Goals** | Why this change is needed; the constraints/goals it optimizes for, in priority order if they trade off against each other |
| **Specification** | Normative behavior: formats, algorithms, interfaces, validation rules, test expectations. This is the section that must let someone implement it. |
| **Security Considerations** (or **Risks & Considerations** outside security-sensitive domains) | New attack surfaces / failure modes, trust assumptions, interactions with existing invariants. If there's no impact, say so explicitly and why — don't omit the section. |

Optional sections worth adding when relevant: **Alternatives Considered** (informative),
**Rollout / Migration**, **Test Vectors**, **Appendices** (proofs, benchmarks) — mark
non-normative material as such so it's clear it's not part of the spec being reviewed.

## 3. Write for independent, compatible implementations

An RFC MUST be precise enough that two people, working independently, produce compatible
results. The specification SHOULD define, whichever apply to this change:

- exact object/message/wire formats, or API contracts and data schemas;
- field names, types, encoding, limits, defaults;
- ordering rules for lists, maps, events, IDs;
- required validation, rejection, and fallback behavior;
- versioning and backward-compatibility rules;
- test vectors or conformance cases for edge cases and invalid input.

If a reader must infer behavior from an implementation, an issue thread, a discussion, or
"how the database happens to be laid out," **the RFC is underspecified** — fix the spec, not
the reader's assumptions.

## 4. Style

- Use RFC 2119 requirement words consistently: **MUST**/**MUST NOT** (required),
  **SHOULD**/**SHOULD NOT** (recommended, valid exceptions exist), **MAY** (optional).
- Keep RFCs concise: short normative statements, tables, and examples beat long prose.
  Concise doesn't mean short — it means every sentence carries spec meaning.
- When editing, remove ambiguity before adding detail.

## 5. Lifecycle (default status vocabulary — use the repo's own if it already has one)

`draft` → `open-for-discussion` → `last-call` → `approved` | `rejected` → (optional)
`implemented`

- **draft** — first pushed as a PR, still taking shape.
- **open-for-discussion** — actively under review; changes still expected. Link the
  discussion thread (GitHub Discussions/issue) from the RFC.
- **last-call** — final review window before approval barring a major objection.
- **approved** — accepted; execution/implementation pending if applicable.
- **rejected** — stopped; say why in the revision history rather than deleting the doc.
- **implemented** (optional) — the change described has landed.

**Dependencies:** an RFC listing another RFC as a dependency does not advance past
`last-call` until that dependency is `approved`. If the dependency is `rejected`, this RFC
is `rejected` too unless revised to drop the dependency.

## 6. Before calling it done

- Every normative claim traces to something a reader could actually verify or test.
- The Security/Risk Considerations section isn't a placeholder — it names real surfaces or
  explicitly states there are none, with why.
- Metadata table and status reflect where the RFC actually is, not where you expect it to
  land.
- If this repo has a rule to keep other docs in sync with specs (e.g. an explorer UI, a
  protocol spec directory) check whether this RFC's change requires updating those too —
  many repos treat a stale cross-reference as a bug, not a nice-to-have.

See `template.md` in this skill for a copy-paste starting structure.
