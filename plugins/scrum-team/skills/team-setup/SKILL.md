---
name: team-setup
description: Sets up the scrum-team plugin in a repository by writing .claude/scrum-team.md (architecture sources, standards, build and test commands, Definition of Done, human review paths, parallelism rules) and the backlog folder with an empty board. Use when the user says "set up the scrum team", "initialise the agents for this project", or when the sprint skill finds no config. Do NOT use to plan work; that is the sprint skill.
metadata:
  version: 2.1.0
---

# Team setup

## Critical rules
- Never overwrite an existing `.claude/scrum-team.md` or backlog. If they exist, show what you would change and ask.
- Every command you put in the config must be one you verified exists in the repo (project files, package scripts, solution files). Unknown → leave a `TODO` and ask.

## Steps
1. **Inspect the repo, lean:** root and nested `CLAUDE.md`, README, solution and project files, `package.json` scripts, existing docs folders. Don't read source files in bulk.
2. **Draft the config** from `assets/scrum-team-config-template.md`, filling in:
   - ticket key (ask the human; suggest one from the project name),
   - architecture sources and code-standards sources (existing docs and CLAUDE.md files),
   - commands per area (build, test, format check, lint, architecture tests),
   - project Definition of Done,
   - code standards (keep the template's defaults unless the project's docs say otherwise),
   - sensitive code (template defaults plus project areas the human names) and human review paths (CI workflows, billing, auth, migrations…), plus extra rules for the risk classifiers,
   - parallelism (areas that can run side by side, max developers),
   - commit convention,
   - official docs domains developers should check.
3. **Show the draft** to the human as a compact summary and apply their corrections.
4. **Write** `.claude/scrum-team.md`, `<backlog_path>/BOARD.md` (empty board, per the sprint skill's board template) and `<backlog_path>/tickets/`.
5. **Usage warning (optional, user settings).** The plugin warns at 75% context or 5-hour plan usage, but only Claude Code's status line receives those numbers, and a plugin cannot set the status line. Read `~/.claude/settings.json`:
   - No `statusLine` → show the human this entry and ask before writing it (use the absolute path from `command -v scrum-team-statusline`):
     `"statusLine": {"type": "command", "command": "python3 -I <absolute path>/scrum-team-statusline"}`
     Write it only if they say yes, keeping every other setting. Tell them it shows `ctx 42% · 5h 18%` and takes effect in new sessions.
   - A `statusLine` already exists → never replace it. Explain that their script can pipe its JSON input into `scrum-team-statusline` to keep the warning.
   - The path contains the plugin version, so re-run this step after a plugin update if the status line goes blank.
6. **Tell the human** the next step: `/scrum-team:sprint` with a goal.

## Example
User: "set up the scrum team here" → finds `backend/*.slnx` and `frontend/package.json` → proposes key `BIL`, commands `dotnet test`, `npm test`, parallelism "1 backend + 1 frontend" → human confirms → files written.

## Troubleshooting
- **No build or test commands found** → write `TODO` in the config, and make "agree build/test commands" the first ticket.
- **Monorepo with many projects** → one config per project folder; start Claude Code in that folder.
