# Handoff

The working state for whoever picks up next. Keep it short and current: replace it when a task ends, don't append a log.

**Updated:** 2026-10-19.

## Current focus

None in progress. The last two tasks are committed on `main`, not pushed:

- Google BigQuery (`082a2e1`): REST API, service account keys, datasets as databases, typed edits. Tried against the emulator; sign-in to Google only unit-tested.
- SQL Server's gaps from TODO §24: named instances through SQL Browser, functions, procedures and sequences in the sidebar with editors, column defaults, comments and triggers in the structure editor. Tried in ignored tests against SQL Server 2022; the named-instance lookup against a stand-in SQL Browser, not a real one.

## What needs the owner

The open questions are Q-001 to Q-006 in [../KNOWN-ISSUES.md](../KNOWN-ISSUES.md#open-questions-for-the-owner). Remaining engine work: Snowflake, Redshift, Greenplum, Vertica (no Docker images to test against), and each engine's "Not done" list in TODO §24.
