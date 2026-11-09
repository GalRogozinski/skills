---
name: pr-review
description: Use when reviewing a pull request, responding to review comments on one, or verifying and fixing what reviewers found — in any repo. Covers reading the existing thread before writing, the comment format (issue → fix → explanation), which review skills to run for code vs RFC/spec changes, dispatching an Opus agent (xhigh effort) and a later codex agent, verifying every claim before asserting it, and presenting all conclusions for approval before anything is posted.
---

# PR Review

Reviewing a PR, or handling review comments on your own PR. Applies to any repo.

## 0. Orient before writing anything

**Read the whole thread first.** Fetch every comment *and every reply*, oldest first:

```bash
gh pr view <n> --repo <owner/repo> --json title,body,headRefName,baseRefName,state
gh api repos/<owner/repo>/pulls/<n>/comments --paginate \
  --jq '.[] | {id, path, line, user: .user.login, in_reply_to_id, created_at, body}'
gh api repos/<owner/repo>/issues/<n>/comments --paginate --jq '.[] | {user: .user.login, body}'
```

`in_reply_to_id` tells you which threads already have answers. Never raise a point that is
already raised, already answered, or already fixed in a later commit — check the commit log
against each comment before writing.

**Read the repo's `CLAUDE.md` and `AGENTS.md`** (root and any nested ones covering the changed
paths). They override your defaults: build/test commands, which files are generated, project
conventions.

**Collect context caveats.** Files that are templates, doc-like specs, stubs, generated code,
or vendored. Write them down — every agent you dispatch must be told, or it will report the
stub as a bug and bury the real findings.

**Work in an isolated worktree** for any fix you commit — see the git rules in global
`CLAUDE.md`.

## 1. Pick the lane by what changed

| Changed | Run |
|---|---|
| Code | `/code-review`, `/security-review`, `/ponytail:ponytail-review` |
| RFC / design / spec `.md` | `/rfc`, plus a deliberate security pass |
| Both | All of the above, code first |

For an RFC, ambiguity *is* the defect: two implementers reading it must not build different
things. Read it as an attacker too — what does an unspecified case let a malicious party do?

## 2. Agents: Opus first, codex second

- **Always dispatch an Opus agent at xhigh effort** to review independently: Agent tool with
  `model: "opus"` and `effort: "xhigh"`. Never skip it, even if you are Opus yourself: it runs in
  the background while you keep working, and it gives a read not anchored on yours.
- **Always dispatch a codex agent afterwards**, and give it *both* the code and the Opus
  review to critique. Order matters: codex reviewing Opus's conclusions is how an
  incomplete-but-plausible fix gets caught.
- Brief both with the context caveats from step 0, plus anything already known and accepted,
  so they don't re-flag it.
- **Never take an agent at face value.** Reproduce what it claims. A confident agent report is
  a hypothesis, not a finding — and a *suggested fix* from any source can be correct for the
  reported vector while still incomplete.

## 3. Comment format

Three blocks, in this order. No preamble, no praise sandwich.

```
**<One or two lines: what is wrong.>**

Fix: <what to change, concretely. A ```suggestion block if it is small.>

<A few lines: why it matters, the mechanism, and your evidence.>
```

Rules:
- Any CS student should follow it. Every term of art gets a short clause explaining it.
- Laconic and complete are not in tension — cut adjectives, keep facts.
- Give evidence, not adjectives: the failing input, the output you observed, the mutation that
  survived. "This is fragile" is not a finding.
- State severity honestly, and say when something is non-blocking or defence-in-depth.

## 4. Verify before you assert

- **Reproduce first.** Write the throwaway test that shows the bug before proposing a fix.
  Delete it afterwards.
- **Mutation-test every new guard.** Remove it; a test must fail. A guard no test pins is
  undefended, and an unreachable guard cannot be pinned at all — say which it is.
- **Root cause, not the reported call site.** Grep every caller before editing. One guard in
  the shared function beats a guard in each caller.
- **Check the fix excludes nobody legitimate.** A new rejection path must not reject a valid
  caller; work out who reaches it.
- **For an API change, check the real consumers.** A guard added to a function nobody calls
  guarantees nothing. Find the actual call sites in the consuming repo.
- **Watch for over-claiming.** A test asserting a path that never failed proves nothing, and a
  comment promising a property the code cannot deliver is worse than no comment. If a check is
  consistency rather than authentication, say so in the comment.
- **Beware shared fixtures.** Package-level test slices and structs mutate globally — build a
  fresh copy per case.

## 5. Present conclusions, then ask before posting

**Never post to a PR without permission.** When the review is done, print the full set of
conclusions in the terminal and stop:

- Every finding, most severe first: the issue, the fix, the severity, and how you verified it.
- Every comment you are answering, with the reply you intend to post.
- What you are rejecting or leaving alone, and why.
- What Opus and codex each concluded, including where they disagreed with you or each other.

Then ask for permission to post. Only after the user approves do you post anything — inline
comments, replies, or a review summary. The same gate applies to pushing fixes to someone
else's branch.

## 6. Close every thread

- Answer every comment, including the ones you are rejecting and the nits.
- Each reply: what you did, the commit SHA, and how you verified it.
- Say plainly what you deliberately did **not** change, and why — an unmade change that the
  reviewer expected reads as an oversight unless you name it.
- Reply into the existing thread, not as a new comment:

```bash
gh api repos/<owner/repo>/pulls/<n>/comments/<comment_id>/replies -f body='...'
```

- Report faithfully. If a fix is partial, or a test only covers the validator and not the wire
  path, write that.
