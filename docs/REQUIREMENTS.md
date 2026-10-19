# Requirements

The canonical list of what TableMinus must do and must not do. Feature-level detail lives in [../TODO.md](../TODO.md); this file holds the requirements behind it, especially the ones the owner stated in conversation that TODO.md doesn't spell out.

Each entry: the requirement, its source, and its status. **Source** is where it was stated: *owner* (said in a session, with the date), *TODO* (TODO.md, written from TablePlus at the owner's request), *rules* (CLAUDE.md, now [CONVENTIONS.md](CONVENTIONS.md)). **Status:** Done (in code on `main`), Partial, Not started, Rejected, Open (undecided). "Done" means implemented; how far it was tried is in TODO.md.

## Product scope

| ID | Requirement | Source | Status |
| --- | --- | --- | --- |
| REQ-001 | Everything TablePlus 27.0.2 does with databases, feature for feature ("I want everything to be same functionality wise"). | owner 2026-10-13 | Partial: see TODO.md, most sections `[x]` |
| REQ-002 | Runs on macOS and Linux. | owner 2026-10-13 | Partial: macOS in daily use; Linux `.deb`/AppImage build in Docker but haven't been run on a Linux desktop |
| REQ-003 | Rust core (Tauri). | owner 2026-10-13 ("go with Rust, personal tool") | Done |
| REQ-004 | Don't copy TablePlus's licensing, trial, purchase, register, iOS promo, Sentry or bug-report screens. | TODO "Not copying" | Done (none exist) |
| REQ-005 | The engines in TODO §24: Postgres, MySQL, MariaDB, SQLite, Redis, CockroachDB, DuckDB, SQL Server, ClickHouse, libSQL, D1, MongoDB, DynamoDB, Elasticsearch, Cassandra, Oracle; then Redshift, Greenplum, Vertica, Snowflake, BigQuery. | TODO §24 | Partial: the first 16 and BigQuery exist at varying depth; the other four not started |

## Connections and workspaces (stated by the owner)

| ID | Requirement | Source | Status |
| --- | --- | --- | --- |
| REQ-020 | Connect without naming a database, then explore the server's databases (as TablePlus does). | owner 2026-10-13 | Done (⌘K database picker) |
| REQ-021 | Every connection-form field is optional; each engine's defaults show as placeholders. | owner 2026-10-13 (recorded in that session's summary) | Done |
| REQ-022 | Several databases open at once, each as its own tab, to switch between easily. | owner 2026-10-14 | Done (workspace tab strip) |
| REQ-023 | In the open-database dialog: **Enter** opens the database in the current tab, **⌘Enter** in a new tab. Supersedes the 2026-10-15 request that choosing another database should always open a new tab. | owner 2026-10-17 | Done |
| REQ-024 | ⌘N / New workspace must not replace the open workspace: a new window, or "a good system to switch between different opened workspaces". Opening a connection that already has a workspace opens a new tab, not the existing one. | owner 2026-10-15, 2026-10-17 | Done (workspace tabs; a new workspace per connect) |
| REQ-025 | Closing the last item tab keeps the connection open (TablePlus closes it; the owner overrode this). | owner 2026-10-15 | Done |
| REQ-026 | The sidebar's ‹ Connections shows the connections without closing the current one. | owner 2026-10-15 | Done |
| REQ-027 | Double-click opens a connection on the launcher; single click focuses it with a visible focus border. | owner 2026-10-17 | Done |
| REQ-028 | After connecting, the open-database dialog appears after a 1 s delay (owner first asked for 2–3 s, then "it's too long, reduce to 1s"). | owner 2026-10-17 | Done (`PICKER_DELAY_MS = 1000`, `src/workspaces.ts`) |
| REQ-029 | Esc closes the open-database dialog. | owner 2026-10-17 | Done |
| REQ-030 | Find local databases automatically and save connections for them; flag the ones whose default credentials failed with a badge, asking for credentials. | owner 2026-10-17 | Done (`connect/discover.rs`) |
| REQ-031 | Postgres connections list functions, extension functions included. | owner 2026-10-17 (bug report) | Done |

## Editor, tabs and files (stated by the owner)

| ID | Requirement | Source | Status |
| --- | --- | --- | --- |
| REQ-040 | CodeMirror with syntax highlighting and line numbers. | owner 2026-10-13 | Done |
| REQ-041 | Inline cell editing in the data grid. | owner 2026-10-13 | Done |
| REQ-042 | SQL tabs save automatically, and when the app or connection closes; reconnecting to the same database brings its own SQL tabs back. | owner 2026-10-15 | Done |
| REQ-043 | An option to open the folder where a file was saved (Show in Finder). | owner 2026-10-15 | Done |
| REQ-044 | Resizable left sidebar, SQL editor/results split and console. | owner 2026-10-15 | Done |
| REQ-045 | Saved queries are favorites from history: a section in the History pane, not their own crowded tab. | owner 2026-10-15 | Done |

## Look and feel (stated by the owner; more in [UI-UX.md](UI-UX.md))

| ID | Requirement | Source | Status |
| --- | --- | --- | --- |
| REQ-060 | A custom title bar instead of the native empty strip, with movable items in it. | owner 2026-10-15, 2026-10-17 | Done (macOS overlay title bar) |
| REQ-061 | Instant tooltips on icon buttons. | owner 2026-10-17 | Done (`src/tip.ts`) |
| REQ-062 | Readable shortcut hints, visible icons, one consistent look for input fields, a styled find/replace panel. | owner 2026-10-15 | Done (commit `751c74b`) |
| REQ-063 | New logo: a blue mockingbird (*Melanotis caerulescens*), flat, readable at 16px. | owner 2026-10-15 | Not started: one attempt was reverted (DEC-021) |

## Safety and data (rules and TODO)

| ID | Requirement | Source | Status |
| --- | --- | --- | --- |
| REQ-010 | Passwords and other secrets live only in the OS keychain (or the encrypted vault on Linux without a Secret Service), never in `connections.json`, logs or URLs. | rules, TODO §1 | Done |
| REQ-011 | Safe mode: TablePlus's five levels, per connection, enforced in the backend for every path that runs SQL (editor, grid saves, DDL, imports, MCP, AI, plugins, scripts). | TODO §18 | Done ([features/safe-mode.md](features/safe-mode.md)) |
| REQ-012 | Never lose user data on an error: refuse to overwrite a file that failed to load; write to a temporary file, then rename. | rules | Done (code-wide) |
| REQ-013 | Values reach SQL as bound parameters; only identifiers and DDL string literals are escaped by hand. | rules | Done |
| REQ-014 | Cap anything untrusted that drives an allocation or a loop. | rules | Done (limits listed in TODO §26) |
| REQ-015 | Every network call has a timeout. | rules | Done |
| REQ-016 | App passcode lock that the backend enforces, the MCP server included. | TODO §18 | Done |

## Process requirements (the owner's standing instructions)

These bind every agent; [agent-context/WORKFLOW.md](agent-context/WORKFLOW.md) has the detail.

| ID | Requirement | Source |
| --- | --- | --- |
| REQ-090 | No AI attribution anywhere: no `Co-Authored-By`, `Claude-Session` or "Generated with" lines in commits, PRs, comments or files. Commits are authored as `Ritesh Shrestha <shrestha.ritesh@hotmail.com>`. | owner (global and project CLAUDE.md) |
| REQ-091 | Nothing leaves the machine without explicit permission for that specific action, given just before: no `git push`, PRs, releases, published pages or external writes. Local commits are fine. | owner (global CLAUDE.md) |
| REQ-092 | `npm run check` passes before every commit. | rules |
| REQ-093 | When testing against a database that already exists, don't change it without taking a backup first. | owner 2026-10-17 |
| REQ-094 | Follow the three Rust guides the owner named (Apollo, Corgea, kobogithub). | owner 2026-10-13 |

## Rejected or deferred

| ID | Item | Status |
| --- | --- | --- |
| REQ-R01 | Manual drag-and-drop ordering of launcher connections. Built on 2026-10-17, and the owner said "no actually revert it" within a minute; reverted. TODO §1 still lists "manual ordering" as not yet done. Ask before building it again. | Rejected (for now) |
| REQ-R02 | An HTTP MCP server with TLS for remote access. | Deferred: "out of scope for now" (TODO §20) |
| REQ-R03 | Tailwind CSS. The owner asked whether it could be used; the agent advised against it ([DECISIONS.md](DECISIONS.md) DEC-020) and the owner didn't decide. | Open |
