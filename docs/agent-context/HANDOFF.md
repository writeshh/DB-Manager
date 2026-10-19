# Handoff

The working state for whoever picks up next. Keep it short and current: replace it when a task ends, don't append a log.

**Updated:** 2026-10-19.

## Current focus

None in progress. The last task, checking the untried features in the app window (TASK-005), is committed on `main`, not pushed. It drove a debug `.app` (`npm run tauri build -- --debug --bundles app --no-sign`, run from inside the bundle with `ORACLE_CLIENT_LIB_DIR` set) against `scripts/test-env.sh`, with the owner's permission for computer use.

- Eight bugs found and fixed (ISSUE-031); four noted, not fixed (ISSUE-032 to ISSUE-035).
- Test connections named "TM test …" were added to the owner's connection list for the check (the debug build shares the installed app's data folder). Ask before deleting them.
- Not checked: the Needs credentials badge, the metrics board's auto-refresh, lock, resize, boards and import/export.

## What needs the owner

The open questions are Q-001 to Q-006 in [../KNOWN-ISSUES.md](../KNOWN-ISSUES.md#open-questions-for-the-owner).
