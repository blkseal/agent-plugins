# scrum-team

A Scrum team for any repository. The Scrum Master skill (`/scrum-team:sprint`) plans tickets
with acceptance criteria and a Definition of Done, gets human approval, and delegates each one to
a `developer` paired with a `pair-reviewer` that reads every checkpoint as the work happens. The
commit gate then runs the `code-auditor` (AI code review), the `security-reviewer` when the code
review flags it, and three `risk-classifier` votes on whether a human must look (skipped when the change
clearly cannot need one); when a human must look, the Scrum Master asks them in chat. A PreToolUse hook
blocks `git commit` unless the reviewers stamped the exact staged diff.

Setup per repository: `/scrum-team:team-setup` writes `.claude/scrum-team.md` (architecture
sources, standards, commands, DoD, human review paths, parallelism) and `docs/backlog/`. Full
instructions are in the marketplace README one folder up.
