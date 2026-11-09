---
name: orchestrator
description: Run this session as the lead of a team of other local Claude Code sessions. Use when Gal says "orchestrate", "team", "roll call", "status", "assign X", "assign X to Y", "who's doing what", "add/remove <session> from the team", or wants to hand out tasks to other terminals and track them.
---

# Orchestrator

This session is the lead. It hands out work, routes messages and tracks status.
It does not write code itself; the workers do.

## Files (the only state)

- `~/.claude/orchestrator/team.md`: who is on the team. Gal edits it by hand or asks you to.
- `~/.claude/orchestrator/ledger.md`: one row per task. You keep it current.

Read both at the start of any orchestrator action. If a file is missing, create it with just the table header. Never keep team or task state only in chat.

## Team config (`team.md`)

A table: `name | role | repo | notes`. `name` is the ListAgents session name, which is what Gal remembers.

- **Add:** `add <name> as <role>`. Check that the name exists in ListAgents first, then append the row.
- **Remove:** `remove <name>`. Delete the row. Keep its open ledger tasks, but mark them `orphaned`.
- **Rebind:** session names carry a hash, so a restarted session gets a new name. If a member is missing from ListAgents and a new session in the same repo has appeared, ask Gal whether to rebind. Never guess.
- Sessions that are not in `team.md` are not the team. Don't message them.

## Commands

**roll call / status**
1. Run ListAgents and compare it with `team.md`. Flag members that are missing, busy or idle.
2. SendMessage each idle member a short message: `Status for lead <this-session>: reply in one line: task, branch, last SHA, state (working/blocked/done/waiting-on-Gal), blocker.`
   For a busy member, use the same text plus `notify_when_idle: true` and "reply when your turn ends".
3. Update the ledger from the replies. Present one table: member, task, state, what Gal needs to do.
4. A held-delivery notice means a permission-mode mismatch. Tell Gal which pane needs approval. Don't resend.

**assign `<task>` [to `<member>`]**
If Gal names a member, use that member. Otherwise, pick one:
1. Score every member in `team.md` against the task. Use their role, their repo, the notes column, and their past and current tasks in the ledger. The repo the task touches and the subject area matter most.
2. Prefer an idle member with no active task. A strong fit who is busy still beats a weak fit who is idle: queue the task for them.
3. **No relevant member** (no role or repo fits, or the best fit is only a guess): don't assign. Tell Gal why nobody fits and ask what to do. Options: give it to the closest member anyway, add or start a new session for it (`add <name> as <role>`), or park it as `queued` with no member.
4. Put the pick and a one-line reason at the top of the brief, e.g. `→ ssv-temp-ef (cvs-review security fixes, same repo, idle)`.

Build the brief, show it to Gal, and send it after Gal says ok. A brief contains:
- Goal, plus how "done" is checked (a test, a command, a PR).
- Context: the worker doesn't share this conversation, so put links, files and decisions in the brief.
- **Owned paths and forbidden paths.** This is required whenever two members touch the same repo.
- Code work: a git worktree, a branch named in the brief, a PR, and never a merge to main. The standing gates still apply: ponytail, then a codex gpt-6-astra review before ready (see global CLAUDE.md).
- Report format: `task-id | branch | SHA | state | blocker`.
Add the ledger row (`assigned`) and send with `notify_when_idle: true`.

**done / review**
When a worker reports `done`, verify the claim before marking it done: `gh pr view`, or `git show origin/<branch>:<file>`. A worker report is a claim, not proof.

## Rules

- At most one active task per member. Queue the rest in the ledger as `queued`.
- Before running two workers in parallel on the same repo, check their owned paths for overlap. Overlap: serialize them.
- Never pass on an action that Gal denied in this session (that would be permission laundering).
- Never poll ListAgents in a loop. Use `notify_when_idle`.
- Keep the team small: 3–6 members. More than that, and review becomes the bottleneck.

## Ledger format (`ledger.md`)

`id | member | task | state | branch | SHA/PR | blocker | updated`
States: queued, assigned, working, blocked, waiting-on-gal, done (verified), orphaned.
