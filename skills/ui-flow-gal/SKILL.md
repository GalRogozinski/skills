---
name: ui-flow-gal
description: Gal's end-to-end flow for any UI change (a new page, a screen, a table layout, a dashboard or console view). It chains a spec (Gherkin acceptance criteria plus a plan), a rendered mock that Gal signs off, implementation in an isolated worker lane, Playwright e2e QA with one test per acceptance criterion, a live sanity check, an independent model review, and a PR. Use it when asked for UI work, a mock, a dashboard or page change, or "ui flow".
---

# ui-flow-gal

One flow for every UI change. The steps run in order, and each has a gate that must pass before the next starts. Skip a step only when the owner explicitly says so.

## 0. Frame it
- Name the surface, the user, and the single job of the change.
- Read the surface's existing design tokens and components first. **The project's design system wins** over any default.
- Check which spec numbers are free (grep the existing specs for the highest story and requirement IDs) and **reserve a range** for this change. Parallel lanes collide on numbering, so write the reserved range into every brief.

## 1. Spec
- Use the `sdd` skill. Write `specs/<feature>/spec.md`: user stories, each with a fenced ```gherkin block of 1-4 scenarios (happy path first, then the edge cases that matter), functional requirements, edge cases, success criteria and out-of-scope.
- Write `specs/<feature>/plan.md` beside it: data sources, component structure, states (loading, empty, error, unknown shows "-", never 0), and known limits.
- **Gate:** every story has Gherkin, and every acceptance criterion (AC) is testable in a browser.

## 2. Mock, then sign-off
- Use the `frontend-design` skill. Build the mock as a static HTML page using the surface's REAL tokens, fonts and components, filled with **real data**, never lorem.
- When there is a design choice, render **2 options side by side** with a short trade-off panel and a recommendation.
- Publish it somewhere the owner can see it rendered. Keep the source HTML.
- **Gate:** the owner picks an option or approves. Don't implement before that.

## 3. Implement in an isolated worker lane
- One task per surface, in its own git worktree, based on `main` (or on the feature's PR branch when extending an open PR).
- Copy the approved mock's HTML into the task folder with a short README saying which option was chosen. A link alone is useless to a worker when it sits behind auth.
- Every brief carries these clauses:
  1. **Advice loop:** stop and ask instead of guessing on genuinely ambiguous decisions.
  2. **Minimal diff:** the smallest change that works. **The test bar is exempt: tests in full.**
  3. **Match the design system:** existing tokens and components exactly.
  4. **Commit after every coherent step**, and never hold more than one step uncommitted.
  5. **OWNED and FORBIDDEN paths**, named explicitly. Docs and spec files collide like code, and shared test or tooling files have one owner at a time.
  6. **Ports:** never bind or restart the owner's running dev ports. Use ephemeral ports, and stop only the processes you started. Never `pkill` by name.
  7. **Gates in the FOREGROUND.** A reply means done, with each result line pasted.

## 4. QA: Playwright, one test per AC
- Put the e2e tests beside the surface, copying the repo's existing pattern and `@playwright/test` version.
- Name each test exactly `AC-n: <scenario>`, grouped by user story.
- Keep them deterministic: fixture-backed server or route interception with fixture JSON. Never read live external data in e2e.
- Cover every AC, plus the loading, empty, error and unknown ("-") states. Map each AC to its test name in `spec.md`.
- **Gate:** e2e runs green in the foreground, and unit tests and typecheck pass.

## 5. Live sanity check
- Start the real stack read-only on **ephemeral ports**, load the page, and check the key facts with a DOM or Playwright query against live data. Paste that output.

## 6. Independent review
- Get a static review of the diff from a different model than the one that wrote it (prompt plus `git diff origin/main...HEAD`).
- Send incremental diffs with a prior-findings summary on re-review.
- Fix P0/P1 findings and cheap P2s. **Before round 3, ask the owner where the bar is.** Document residual limits in the spec's out-of-scope section instead of chasing them.
- **Gate:** the reviewer says safe, or the owner accepts the documented residuals.

## 7. Ship
- Rebase on `origin/main`, push the branch, and open a PR.
- Merging and deploying are the owner's call. Never deploy without an explicit go.

## Report after each gate
Say what passed, give the link (mock, PR, local URL), and state the next decision that is the owner's.
