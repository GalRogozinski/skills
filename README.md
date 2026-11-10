# skills

Agent skills in the `SKILL.md` format. They work with Claude Code, and you can add them to
[Rakazo](https://github.com/elie222/rakazo) from **Skills → From GitHub** with this repo's URL.

## Skills and credits

| Skill | What it does | Original author | Source | License |
|---|---|---|---|---|
| `eli-x` | Ends tasks with a plain-language wrap-up and gives hard multi-step work memorable stage names. | Gal Rogozinski | this repo | MIT |
| `pr-review` | Reviews a pull request: read the thread first, one comment format, verify every claim, ask before posting. | Gal Rogozinski | this repo | MIT |
| `rfc` | Drafts and reviews RFCs and design specs so independent implementations stay compatible. Adapted from SSV Labs' SIP process. | Gal Rogozinski, adapted from [SSV Labs SIPs](https://github.com/ssvlabs/SIPs) | this repo | MIT |
| `ui-flow-gal` | Gal's flow for UI changes: spec, mock, worker lane, Playwright QA, review, PR. Generalized from a private version. | Gal Rogozinski | this repo | MIT |
| `orchestrator` | Turns one Claude Code session into a team lead: roll call and status, auto-assigns tasks to the best-fit session, keeps a team roster and task ledger. | Gal Rogozinski | this repo | MIT |
| `humanizer` | Strips AI writing tics from prose. | Siqi Chen ([@blader](https://github.com/blader)) | [blader/humanizer](https://github.com/blader/humanizer) | MIT |
| `subtask` | Orchestrates parallel AI workers in isolated git worktrees. Needs the `subtask` CLI. | zippoxer | [zippoxer/subtask](https://github.com/zippoxer/subtask) | MIT |
| `sdd` | Spec-driven development with GitHub's Spec-Kit, greenfield and brownfield. | SpillwaveSolutions | [SpillwaveSolutions/sdd-skill](https://github.com/SpillwaveSolutions/sdd-skill) | MIT (stated in its README; the repo has no LICENSE file) |

Third-party skills are copied with their license where one exists (`humanizer/LICENSE`, `subtask/LICENSE`).
Check the source repo for newer versions.

## Notes on the copies

- `humanizer` is the current upstream version, not an older local one.
- `sdd` is a locally modified copy (with a "Model roles" section added by Gal), renamed from `skill.md` to `SKILL.md` so Rakazo finds it. Its `scripts/` are not run by Rakazo, which treats GitHub skills as instructions only.
- `subtask` is useless without the `subtask` CLI, and `ui-flow-gal` leans on `sdd`, `subtask` and `frontend-design`. In Rakazo they read as guidance, because bots there don't have these tools.
- `pr-review` mentions `codex` and `/code-review`, which exist only in Claude Code setups.
- `orchestrator` needs Claude Code's cross-session tools (`ListAgents`, `SendMessage`). Its team and ledger live in `~/.claude/orchestrator/`, not in this repo. Sessions in a different permission mode hold its messages for approval.
