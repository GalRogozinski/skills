---
name: orchestrator
description: Run this session as the lead of a team of other local Claude Code sessions. Use when the user says "orchestrate", "team", "roll call", "status", "assign X", "assign X to Y", "who's doing what", "add/remove <session> from the team", or wants to hand out tasks to other terminals and track them.
---

# Orchestrator

This session is the lead. It hands out work, routes messages and tracks status.

**Always assign. Never do the task yourself.** Every task the user gives goes to a team
member: code, reviews, re-reviews, research, lookups, posting, approvals. This holds
even when the task looks like a two-minute job, even when you have the context, and
even mid-thread right after a worker reported. If the user's message is a task and not an
orchestrator command, run **assign** on it. The lead's own work is limited to:
picking the member, writing the brief, sending it, updating the ledger, checking a
worker's "done" claim, and relaying results to the user. If no member fits, ask the user
(see **assign**, step 3). Don't fall back to doing it yourself unless the user says so.

## Files (the only state)

- `~/.claude/orchestrator/team.md`: who is on the team. The user edits it by hand or asks you to.
- `~/.claude/orchestrator/ledger.md`: one row per task. You keep it current.

Read both at the start of any orchestrator action. If a file is missing, create it with just the table header. Never keep team or task state only in chat.

## Team config (`team.md`)

A table: `name | tab | role | repo | notes`. `name` is the ListAgents session name. `tab` is the member's Warp tab name, which is what the user remembers.

- **Tab names:** every member's Warp tab is named by its specialty, kebab-case `<specialty>-<repo>` (e.g. `sec-fixes-api`). `team.md` has a `tab` column. When a member is added or rebound, the user names its tab (in Warp, double-click the tab or right-click > Rename). If Claude Code overwrites it, launch the session with `CLAUDE_CODE_DISABLE_TERMINAL_TITLE=1`.
- **Add:** `add <name> as <role>`. Check that the name exists in ListAgents first, then append the row.
- **Remove:** `remove <name>`. Delete the row. Keep its open ledger tasks, but mark them `orphaned`.
- **Rebind:** session names carry a hash, so a restarted session gets a new name. If a member is missing from ListAgents and a new session in the same repo has appeared, ask the user whether to rebind. Never guess.
- Sessions that are not in `team.md` are not the team. Don't message them.

## Commands

**roll call / status**
1. Run ListAgents and compare it with `team.md`. Flag members that are missing, busy or idle.
2. SendMessage each idle member a short message: `Status for lead <this-session>: reply in one line: task, branch, last SHA, state (working/blocked/done/waiting-on-user), blocker.`
   For a busy member, use the same text plus `notify_when_idle: true` and "reply when your turn ends".
3. Update the ledger from the replies. Present one table: member, task, state, what the user needs to do.
4. A held-delivery notice means a permission-mode mismatch. Tell the user which pane needs approval. Don't resend.

**assign `<task>` [to `<member>`]**
If the user names a member, use that member. Otherwise, pick one:
1. Score every member in `team.md` against the task. Use their role, their repo, the notes column, and their past and current tasks in the ledger. The repo the task touches and the subject area matter most.
2. Prefer an idle member with no active task. A strong fit who is busy still beats a weak fit who is idle: queue the task for them.
3. **No relevant member** (no role or repo fits, or the best fit is only a guess): don't assign. Tell the user why nobody fits and ask what to do. Options: give it to the closest member anyway, add or start a new session for it (`add <name> as <role>`), or park it as `queued` with no member.
4. Put the pick and a one-line reason at the top of the brief. Identify the member by tab name first, e.g. `→ tab "sec-fixes-api" (api-c3): security fixes, same repo, idle`.
   When telling the user where work went, name the tab, then the session name in parentheses.

Build the brief, show it to the user, and send it after the user says ok. A brief contains:
- Goal, plus how "done" is checked (a test, a command, a PR).
- Context: the worker doesn't share this conversation, so put links, files and decisions in the brief.
- **Standing rules from `team.md`** (e.g. the routing rule): paste every rule that applies to the task into the brief word for word. The worker has never seen team.md.
- **Owned paths and forbidden paths.** This is required whenever two members touch the same repo.
- Code work: a git worktree, a branch named in the brief, a PR, and never a merge to main. Any standing gates from the user's global instructions still apply (for example a minimal-diff rule and an independent-model review before ready).
- Report format: `task-id | branch | SHA | state | blocker`.
Add the ledger row (`assigned`) and send with `notify_when_idle: true`.

**done / review**
When a worker reports `done`, verify the claim before marking it done: `gh pr view`, or `git show origin/<branch>:<file>`. A worker report is a claim, not proof.

## Rules

- Always assign (see top). Doing a task in the lead session is a rule break unless the user asked for it.
- At most one active task per member. Queue the rest in the ledger as `queued`.
- Before running two workers in parallel on the same repo, check their owned paths for overlap. Overlap: serialize them.
- Never pass on an action that the user denied in this session (that would be permission laundering).
- Never poll ListAgents in a loop. Use `notify_when_idle`.
- Keep the team small: 3–6 members. More than that, and review becomes the bottleneck.

## Ledger format (`ledger.md`)

`id | member | task | state | branch | SHA/PR | blocker | updated`
States: queued, assigned, working, blocked, waiting-on-user, done (verified), orphaned.
