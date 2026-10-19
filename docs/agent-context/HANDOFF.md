# Handoff

The working state for whoever picks up next. Keep it short and current: replace it when a task ends, don't append a log.

**Updated:** 2026-10-19.

## Current focus

None in progress. The last task cleaned up the old branches (2026-10-19): cleared about 72 GB of build output, removed the four worktrees, merged the `main.ts` split (TASK-001) and landed the live-test runner (TASK-002). Committed on `main`, not pushed.

- The split was merged from `claude/split-main-ts`, then `main`'s 37 commits of `main.ts` changes (new engines in the connection form, document views, empty tabs, the SQL viewer, updates) were ported into the modules by hand. Checked in the browser preview with a stubbed backend (launcher, connect, the MongoDB and CockroachDB forms); not checked in the app window.
- `src-tauri/src` was grouped into domain folders (pure moves, `aedbfd3`); a workspace split was measured and not done (TASK-018).
- The old branches are still there (TASK-003); nothing on them is unmerged.
- Earlier, from the TASK-005 check: test connections named "TM test …" are in the owner's connection list (the debug build shares the installed app's data folder). Ask before deleting them. Not checked in the app window: the Needs credentials badge, the metrics board's auto-refresh, lock, resize, boards and import/export.

## What needs the owner

The open questions are Q-001 to Q-006 in [../KNOWN-ISSUES.md](../KNOWN-ISSUES.md#open-questions-for-the-owner).
