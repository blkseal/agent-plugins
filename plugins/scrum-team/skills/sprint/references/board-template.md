# Board template

File: `<backlog_path>/BOARD.md`. One table, the single overview of the sprint. Tickets hold the detail.

```markdown
# Board

Sprint: <n> · Goal: <one line> · Updated: <yyyy-mm-dd>

| Id | Title | Type | Status | Owner | Depends on | Area |
| --- | --- | --- | --- | --- | --- | --- |
| BIL-1 | Create solution skeleton | chore | Done | developer-1 | — | backend |
| BIL-2 | Add catalogue schema and seed | feature | In Review | developer-1 | BIL-1 | backend |
| BIL-3 | App shell and design tokens | feature | In Progress | developer-2 | — | frontend |

## Open questions
- Q: <question> (blocks BIL-n) · asked <date>
```

Rules: update the row every time a ticket's status changes. Sort by status (In Progress,
In Review, Awaiting Gate, Changes Requested, Ready, Blocked, Backlog, Done), then by id. Move Done rows of
past sprints to `<backlog_path>/archive/sprint-<n>.md` when a sprint closes.
