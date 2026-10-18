# Handoff

The working state for whoever picks up next. Keep it short and current: replace it when a task ends, don't append a log.

**Updated:** 2026-10-19, by the session that created `docs/`.

## Current focus

None in progress. The last feature work, Oracle through Instant Client, is committed (`12be348`, 2026-10-18; check and audit passed).

## Latest task: the documentation system

- **Asked:** a tool-independent documentation system that keeps project context across AI tools and sessions.
- **Done:** created `docs/` (this folder), `AGENTS.md`; moved the coding rules from `CLAUDE.md` to `docs/CONVENTIONS.md` (verbatim), with `CLAUDE.md` now importing `AGENTS.md` and `docs/CONVENTIONS.md`; pointed `README.md` and `TODO.md` §26 at the new locations and refreshed the README's outdated engine line and Layout table.
- **Sources used:** the repository at `12be348`, all 178 commits, the branches and worktrees, the Claude Code transcripts in `~/.claude/projects/-Users-ritesh-Prjkts-Personal-db-manager*` (2026-10-12 to 2026-10-18), the Claude desktop app's side-session notes, and the agent memory note on `oracle-rs`. Not available: the TablePlus internals report (a private claude.ai artifact, not opened), and other tools' histories (none found).
- **Verified:** `npm run check` passes at `12be348`; commands in DEVELOPMENT.md come from `package.json`, `scripts/` and the CI workflow; ISSUE-003 and ISSUE-004 read in the code; trial merges of the two unmerged branches.
- **Committed** on `main` (2026-10-19), not pushed.

## What needs the owner

The open questions are Q-001 to Q-006 in [../KNOWN-ISSUES.md](../KNOWN-ISSUES.md#open-questions-for-the-owner). The most consequential:

- Q-001: what to work on next (no priority set among TODO.md's remaining items).
- Q-005: redo the main.ts split now (TASK-001)?

## Next recommended action

Ask Q-001, or, under the standing instruction "continue with all the leftover todos", pick a ready task from [../TASKS.md](../TASKS.md) (TASK-007 and TASK-008 were started in a separate session on 2026-10-19; check its branch before redoing them) and say which one you picked.

## Before continuing

- Read [WORKFLOW.md](WORKFLOW.md): no pushing without permission, no attribution, `npm run check` before commits.
- Local `main` is ahead of `origin/main` (`git status -sb`). Don't push without the owner's go-ahead.
