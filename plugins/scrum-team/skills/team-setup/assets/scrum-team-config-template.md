# Scrum team config

Read by the scrum-team plugin (Scrum Master skill and all scrum-team agents).
Paths are relative to this repository folder. Keep it short: link to docs, don't copy them.

## Project
- Name: <project>
- Ticket key: <KEY>
- Backlog: `docs/backlog/` (board `BOARD.md`, tickets in `tickets/`)
- Specs (for large epics): `docs/specs/`
- Context the Scrum Master reads when planning: <paths>

## Architecture sources (the pair reviewer and architect enforce these)
- <path> — <what it defines>

## Code standards (the single source: every agent reads these; the pair reviewer and code auditor enforce them)
- Smallest complete change: reuse an existing helper or pattern, then the standard library, then an installed dependency, before writing new code. No dependency for a few lines. No options, wrappers or "for later" code nobody asked for. Finish every part the ticket needs (callers, tests, fixtures).
- Clean: intention-revealing names, small single-purpose functions, no dead or commented-out code, no duplication, no magic numbers.
- Modern: current idioms of the language and framework versions in use; no deprecated APIs.
- Comments: doc comments on public APIs only (purpose, constraints, the business rule ID). Elsewhere only the *why* the code cannot show, in one line. A known limit gets `shortcut: <the limit>, <when to upgrade>`. English.
- Tests: non-trivial logic (a branch, loop, parser, money or security code) gets tests for the happy path, boundaries and errors in the acceptance criteria. Each test can fail: it asserts the real outcome and does not mock the code under test. Trivial changes (copy, config values, renames) need none.
- Safe, never cut: validation at trust boundaries, parameterised queries, secrets in configuration, no personal data in logs, error handling that prevents data loss, accessibility.
- <path> — <project standards doc and what it defines>

## Sensitive code (the code auditor sends these to the security review; the Scrum Master gates them without waiting for a batch)
- Input parsed from users or external systems; authentication and authorisation; secrets and crypto; SQL or other query building; file, shell or network access built from input; new dependencies; CI workflows.
- <project-specific areas, e.g. payments, `src/billing/**`>

## Commands
| Area | Build | Test | Format check | Lint | Architecture tests |
| --- | --- | --- | --- | --- | --- |
| <area> | <cmd> | <cmd> | <cmd> | <cmd> | <cmd or —> |

## Definition of Done (every ticket)
- [ ] Acceptance criteria met, with tests for non-trivial logic
- [ ] Build, format, lint and tests green for touched areas
- [ ] Pair reviewer: READY · Commit gate passed in its batch (code review, security review if flagged, human if required)
- [ ] Docs and contracts updated when behaviour or API changed
- <project-specific items>

## Human review
Paths that always need a human approval before commit, one git glob per line in backticks
(the commit hook reads these lines):
- `.github/workflows/**`
- <e.g. `src/billing/**`, `**/migrations/**`>

Extra rules for the risk classifiers (when a human must look): <e.g. "any change to pricing rules">

## Parallelism
- max_parallel_developers: <n> (each developer runs with its own pair reviewer)
- Areas that may run side by side: <e.g. backend + frontend>
- Never in parallel: <e.g. two tickets that add database migrations>

## Commits
- Convention: <e.g. Conventional Commits `feat(<area>): <summary> [<KEY>-n]`>
- One commit per gate run; it lists every ticket id in the batch.

## Official docs to check for current APIs
- <domain> — <technology>
