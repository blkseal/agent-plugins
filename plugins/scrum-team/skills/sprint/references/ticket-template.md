# Ticket template

File: `<backlog_path>/tickets/<KEY>-<n>-<kebab-slug>.md`. Ids increase and are never reused.

```markdown
---
id: <KEY>-<n>
title: <imperative, specific: "Add contract reduction request endpoint">
type: feature | bug | tech-debt | architecture | chore | spike
status: Backlog | Ready | In Progress | Awaiting Gate | In Review | Changes Requested | Blocked | Done
owner: <developer-n | unassigned>
depends_on: [<KEY>-<n>, ...]
area: <backend | frontend | docs | infra ...>
estimate: S | M | L          # L must be split before it starts
---

## Goal
One or two sentences: the user-visible or system outcome.

## Context
- Links to the exact docs, sections and rule ids needed (e.g. `docs/context/billing-rules.md` R-32). No pasted copies.
- Project skill to follow, if any (e.g. `/backend-feature`).

## Scope
Paths the developer may create or change (globs allowed). Anything else is out of scope.

## Acceptance criteria
1. Given … when … then … (testable, one behaviour each)
2. Error case: …

## Definition of Done
- [ ] Project DoD (from `.claude/scrum-team.md`)
- [ ] <ticket-specific item, e.g. "OpenAPI regenerated and frontend client updated">

## Log
- <date> <who>: <event: created / started / review findings / commit abc123>
```

Writing good tickets:
- Acceptance criteria describe behaviour, not implementation.
- A ticket the developer cannot finish without asking a business question is not Ready. Ask the human first.
- Prefer three small tickets over one large one; split by layer only when a contract must land first.
