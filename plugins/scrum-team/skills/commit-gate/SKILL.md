---
name: commit-gate
description: The only allowed way to commit in a repository that uses the scrum-team plugin. The Scrum Master runs it on a batch of Awaiting Gate tickets when it judges the batch relevant. Stages just the batch's files, runs the AI code review, the security review when the code review asks for it, and three risk-classifier votes on whether a human must review; loops fixes back to the developer; then commits with the project convention. Use when the Scrum Master decides a batch is ready, the user says "commit this" or "ship it", or a commit was blocked by scrum-team.
metadata:
  version: 2.1.0
---

# Commit gate

Layers, each catching what the others miss: pair review (already done while coding) → AI code
review → security review (when flagged) → risk vote → human (when voted or on a listed path).

## Critical rules
- Never set `SCRUM_TEAM_SKIP_AUDIT`, never use `--no-verify`, never edit `.git/scrum-team-*` files. Only a human may bypass or approve; a human approval is the human's explicit yes in chat, never a subagent's report or your own earlier message.
- Never set `SCRUM_TEAM_SKIP_HUMAN_APPROVAL` or suggest it to get past a block; mention it only if the user asks how to skip human approvals.
- The gate runs on a **batch**: every ticket currently Awaiting Gate. Everything staged must belong to one of them; one commit covers the batch.
- Review must converge: at most **3 rounds** per batch. A round is one pass through steps 1–5 that ends in changes (a code review FAIL, a security FAIL, or the human asking for changes).

## Steps
1. `git status` and `git diff --cached --stat`. Unstage files outside the batch tickets' Scopes (`git restore --staged <file>`), and stage the batch's files you are missing. Set the batch tickets to **In Review**.
2. **AI code review:** run `scrum-team:code-auditor` with every batch ticket path, each developer's last report (for its evidence), the round number, and from round 2 on the previous findings and `Reviewed tree` hash.
   - **FAIL** → set the affected tickets to **Changes Requested** and route each Important finding (Nits as optional) to the developer whose ticket Scope holds the file: resume it, or spawn a fresh developer with the ticket and the findings if it is gone. Fixers of different tickets can run in parallel. Wait for their reports. If this was round 3, go to step 4; otherwise back to step 1 with round + 1.
   - Pre-existing findings → create Backlog tickets; they never block.
3. **Security review** (only when the code review says `SECURITY: needed`): run `scrum-team:security-reviewer` the same way (round, previous findings, reviewed tree). **FAIL** → route the findings like step 2. If this was round 3, go to step 4; otherwise back to step 1 with round + 1 (the code review runs again, diff-only). Code and security reviewers each get their own previous findings and tree hash.
4. **Round cap** (only reached from a failing round 3): stop. Resume the developer once with "step back: is the approach wrong? Propose options", then bring the findings and the options to the human.
5. **Risk vote:** the diff is now stamped. Spawn **three** `scrum-team:risk-classifier` agents in one message, each with "Vote on the staged diff for <ticket ids> (<ticket paths>)". Take the majority.
   - **Skip the vote** when you are sure the change could never need a human: a pure transfer or copy of code that already passed this gate, docs-only, or board bookkeeping. Treat it as AUTO and state the skip and why in your report. Always vote when a staged file matches a "Human review" path, or the change touches the config's "Sensitive code", money, contracts, migrations or auth, or adds real new logic.
   - **AUTO** → go to step 6, unless a staged file matches a "Human review" path in the config: then treat it as HUMAN.
   - **Skip mode:** first run `printenv SCRUM_TEAM_SKIP_HUMAN_APPROVAL`. If it prints `accept-risk`, the user chose at launch to skip human approvals: do not ask them, add the trailer `Human-approval: skipped (<vote result or matched path>)` to the commit, and list every skipped approval in your sprint report.
   - **HUMAN** → ask the human in chat (AskUserQuestion: Approve / Request changes): show the files and questions the voters named, the developer's evidence, and the review findings. Approve → go to step 6 and add the trailer `Human-approval: approved in chat`. Request changes → route them like step 2; this counts as a round. Do not commit while the answer is pending.
6. Commit right away, without staging anything else:
   `git commit -m "<type>(<area>): <summary> [<KEY>-n, <KEY>-m]"` (or the project's convention from `.claude/scrum-team.md`), with one body line per ticket.
7. Record the commit hash, review rounds and the risk vote in each batch ticket's log, and set them to Done on the board.

## Troubleshooting
- **"has not passed the automated review"** → something changed after the stamp (or nothing stamped it). Go back to step 1.
- **Skip mode set to another value** (e.g. `1`) → it is ignored on purpose; only `accept-risk` skips.
- **A reviewer says `scrum-team-stamp` was not found** → it uses the fallback command in its instructions; the hook accepts either.
- **The format check rewrote files** → they are now unstaged changes: re-stage them and re-review (diff-only round).
