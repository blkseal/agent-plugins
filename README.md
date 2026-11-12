# Agent plugins

A local Claude Code plugin marketplace (`agent-plugins`) with one plugin so far:

## scrum-team

A reusable Scrum team for any repository.

| Role | Component | What it does |
| --- | --- | --- |
| Scrum Master | skill `/scrum-team:sprint` (runs in your main session) | Splits a goal into tickets with acceptance criteria, Scope and Definition of Done; gets your approval; delegates; runs reviews; commits; keeps `BOARD.md` current |
| Developer(s) | agent `scrum-team:developer` | Implements one ticket within its Scope in checkpoints, showing command output as evidence; several run in parallel when scopes don't overlap |
| Pair Reviewer | agent `scrum-team:pair-reviewer` (read-only) | Paired with each developer; reads every checkpoint for correctness, architecture and standards, so problems are fixed while they are cheap |
| Code Auditor | agent `scrum-team:code-auditor` (read-only) | AI code review of the staged diff: findings tagged Important (blocks), Nit or Pre-existing; diff-only re-review; flags when a security review is needed |
| Security Reviewer | agent `scrum-team:security-reviewer` (read-only) | Runs when the code review flags it; exploitable vulnerabilities only |
| Risk Classifier | agent `scrum-team:risk-classifier` ×3 | Three independent votes; the majority decides whether a human must review before commit |
| Architect | agent `scrum-team:architect` (read-only) | Plan reviews and whole-repo sweeps against the written architecture; findings become tickets |
| Commit gate | skill `/scrum-team:commit-gate` + hook + `scrum-team-stamp` | Blocks `git commit` unless the reviewers stamped exactly the staged diff; when the risk vote or a "Human review" path requires it, the Scrum Master asks you in chat before committing |

Supporting skills: `/scrum-team:team-setup` (writes `.claude/scrum-team.md` and the backlog for
a new repo) and `/scrum-team:commit-gate` (the commit procedure).

The Scrum Master is a skill, not a subagent, because it must talk to you (plan approval,
business questions) and spawn the other agents from the main session.

## Install

```bash
chmod +x plugins/scrum-team/bin/*                                   # once, after cloning
claude plugin validate ./plugins/scrum-team
claude plugin marketplace add /path/to/agent-plugins
claude plugin install scrum-team@agent-plugins
```

Inside a session the same works with `/plugin marketplace add ...` and `/plugin install ...`.
After editing the plugin, run `/reload-plugins` or start a new session.

## Use in a project

1. Open Claude Code in the project folder.
2. `/scrum-team:team-setup`: this writes `.claude/scrum-team.md` (architecture sources, code standards, sensitive code, human review paths, commands, DoD, parallelism). If the project already has one, it shows what it would change and asks.
3. `/scrum-team:sprint` + a goal, e.g. "Plan the sprint: catalogue and pricing".

## Notes
- **Parallel developers** share one working tree, so the Scrum Master runs tickets side by side only when their Scope paths and build areas don't overlap. For heavier parallel work, add `isolation: worktree` to a copy of the developer agent; each one then works in its own git worktree and the Scrum Master merges the branches.
- **Agent teams** (experimental, `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`): you can spawn teammates "using the scrum-team:developer agent type". Plugin skills don't load into teammates, and the commit hook still applies.
- **Emergency bypass** of the commit gate, by a human only: `SCRUM_TEAM_SKIP_AUDIT=1 claude`.
- **Usage warning:** at 75% of the conversation's context or of the 5-hour plan limit, a hook tells the Scrum Master to reach a safe stopping point, update the board and warn you. It needs the plugin's status line (`bin/scrum-team-statusline`) in your user settings, because only the status line receives those numbers; `/scrum-team:team-setup` offers to add it.
- **Skip human approvals** (reviews and risk votes still run): start the session with `SCRUM_TEAM_SKIP_HUMAN_APPROVAL=accept-risk claude`. Any other value is ignored. Skipped approvals are recorded as a `Human-approval: skipped` commit trailer and listed in the sprint report. Agents cannot turn it on: it is read from the environment Claude Code was started with.
- The stamps live in `.git/scrum-team-*`, so they are never committed.
- The gate runs per batch: finished tickets wait staged as Awaiting Gate, and the Scrum Master runs the gate when a ticket is relevant on its own or the batch is big enough. One commit per batch.
- Review converges: at most 3 review rounds per batch, then the developer proposes another approach and the human decides.
