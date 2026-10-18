# Session history

What past working sessions did, so their knowledge isn't lost. Durable findings from them have been moved into the canonical docs (requirements, decisions, issues); this page is an index of where things came from.

## Convention

- Don't paste transcripts here. Move what lasts into the right document (a requirement, a decision, an issue, TODO.md) and link it.
- Add a row to the index below for a session that changed the project's direction, made decisions, or left work unfinished. Routine "commit this" sessions don't need one.
- Write a separate file (`YYYY-MM-DD-topic.md`) only when a session produced context that fits nowhere else: an investigation that found nothing, or a long debugging story whose details matter. Include the date, objective, decisions, work done, validation and open items.

## Sources

The table below was rebuilt on 2026-10-19 from the Claude Code transcripts in `~/.claude/projects/-Users-ritesh-Prjkts-Personal-db-manager*` (the main checkout and one folder per worktree), the Claude desktop app's session list, and its side-session notes (`~/Library/Application Support/Claude/side-session-notes/`, mostly merge notes for the worktree branches, all merged since). The transcripts are local to the owner's machine and not part of the repository. No other tool's history (Codex, Cursor, Copilot) was found.

## Index

| Date | Session (Claude Code id) | Objective | Outcome |
| --- | --- | --- | --- |
| 2026-10-12/13 | TablePlus reverse engineering (`899da495`) | Explain TablePlus's internals with evidence; write the report up as a page; then scaffold an app from it | Report published as a private artifact. Owner chose Rust, "personal tool"; scaffold, launcher, connection form, connect without a database (REQ-020), CodeMirror, inline editing |
| 2026-10-13/14 | Roadmap and first parallel run (`acb1c27f`) | "Create a proper todo list. Take TablePlus as reference"; follow three Rust guides; continue the todo with worktrees | TODO.md and the Rust rules; four, then nine worktree sessions merged; multiple database tabs added to the todo (REQ-022) |
| 2026-10-14 | Rebrand (`46d060f4`, `0b27a760`, `ae7fb7f0`) | "Rebrand to Table Minus" (another session: "Table Zero"), then "actually rebrand to TableMinus" | TableMinus |
| 2026-10-14 | Running the app (`0059adc9`) | How to run it | The cargo-on-PATH fix (DEVELOPMENT.md) |
| 2026-10-15 | Remaining todo items (`aaeacf1c`) | What's left; merge the grid and gaps branches | Merged; virtualized grid started |
| 2026-10-15 | Bundling for other Macs (`98cafac1`) | Make the bundle open on friends' Macs; fix the filter input | Ad-hoc signing (DEC-019); filter input fixed |
| 2026-10-15 | UI feedback and restyle (`9db8a0c0`) | The brand restyle, then about 15 "add to todo" UI items from the owner | UI-UX.md's feedback list; all items done later |
| 2026-10-17 | Project review (`1d09847c`) | "review the project", "fix the issues on your findings", merge origin, redo the main.ts split | `2efec09`, merge `f979e28`; split redone on an unmerged branch (TASK-001); Redis race and session gaps noted (ISSUE-003, ISSUE-004) |
| 2026-10-17 | Launcher and title-bar polish (`bb854d69`, `ea4a79a9`, `675306d0`) | The owner's live UI corrections | Double-click to connect, 1 s picker delay, ⌘↩ new tab, Esc, focus borders, tooltips; manual reordering reverted (REQ-R01); Tailwind question (DEC-020) |
| 2026-10-17/18 | Verification and test servers (`67cd9f1a`) | Verify untried features in the app; Docker test servers; local discovery added to todo | Native checks with granted permissions; greyed-out menu items; `scripts/test-env.sh`; Postgres-over-SSH fix |
| 2026-10-17/18 | Engines, part one (`916a28c1`) | "continue with the fixes, don't stop"; the Driver trait refactor; DuckDB, SQL Server, ClickHouse | DEC-005 and three engines; the app-window check was declined |
| 2026-10-18 | Engines, part two (`b7f3f9c5`) | "continue with all the leftover todos" | libSQL, D1, MongoDB, DynamoDB, Elasticsearch, Cassandra, Oracle, the updater, Linux bundles; `oracle-rs` rejected (DEC-009) |
| 2026-10-19 | Documentation (this session) | A persistent, tool-independent context system | `docs/`, `AGENTS.md` |
