# Delegation prompts

Subagents start with no memory of this conversation. Give them paths, not pasted content.
Resume an agent (SendMessage with its id or name) to keep its context; a new spawn starts fresh.

## Developer
```
You are developer-<n>. Implement ticket <KEY>-<n>: <backlog_path>/tickets/<file>.md
Project config: .claude/scrum-team.md (read it first).
Other developers are working in parallel on: <ticket ids + scopes, or "nobody">. Do not touch their scope.
Work in checkpoints and stop after each one with a CHECKPOINT report. Stage only at the end. Do not commit.
```

## Developer: resume with feedback
```
<Pair reviewer | Code review round <r> | Security review round <r>> findings for <KEY>-<n>.
Answer every item: fix it, or give a one-line reason. Then <continue with the next checkpoint |
re-run the checks, re-stage only your files> and report again:
<paste the findings verbatim>
```

## Pair reviewer
First checkpoint (spawn):
```
You are the pair reviewer for <KEY>-<n>: <ticket path>. Scope: <paths>.
Project config: .claude/scrum-team.md. Developer's checkpoint report:
<paste the report>
```
Later checkpoints (resume): `Checkpoint <n> for <KEY>-<n>:` + the report. Final: `Final pass, READY_FOR_REVIEW:` + the report.

## Code auditor
```
Code review round <r> of the staged diff for batch <KEY>-<n>, <KEY>-<m> (<ticket paths>) before commit.
Project config: .claude/scrum-team.md. Each developer's last report (evidence): <paste>
[Round ≥ 2] Previous findings: <paste>. Previous reviewed tree: <hash>.
Use your output format.
```

## Security reviewer
```
Security review round <r> of the staged diff for batch <KEY>-<n>, <KEY>-<m> (<ticket paths>).
Project config: .claude/scrum-team.md. Each developer's last report: <paste>
[Round ≥ 2] Previous findings: <paste>. Previous reviewed tree: <hash>.
Use your output format.
```

## Risk classifier (three in one message)
`Vote on the staged diff for batch <KEY>-<n>, <KEY>-<m> (<ticket paths>). Project config: .claude/scrum-team.md.`

## Architect
Plan review: `Plan review. Draft tickets: <paths>. Check the split, scopes and order against the architecture.`
Sweep: `Architecture sweep of the whole repository. Report ticket proposals only for real violations.`
