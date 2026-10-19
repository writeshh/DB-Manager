# Known issues

Bugs, limitations and open questions, as of 2026-10-19. **Verified** means reproduced or read in the code at `12be348`; **Reported** means from a past session or TODO.md and not rechecked. Engine and feature limitations that TODO.md already lists under "Not done" aren't repeated here. Read TODO.md §24 for those.

## Bugs and gaps

**ISSUE-001 — Stale descriptions of the three-engine app.** Verified. `src-tauri/Cargo.toml` `description` says "Postgres, MySQL/MariaDB and SQLite". The README's engine line and Layout table said the same until this documentation pass updated them. Fix: TASK-009.

**ISSUE-002 — `src/main.ts` was 3,853 lines.** Fixed 2026-10-19: split into modules, now about 1,000 lines (TASK-001; [ARCHITECTURE.md](ARCHITECTURE.md#the-ui)).

**ISSUE-003 — Saving a connection doesn't close its open non-SQL sessions.** Fixed 2026-10-19 in `f2dacb4`, the commit that adds `Sessions::remove_all`: `save_connection` now drops every session of the connection, under its new and previous name and its database tabs included, from `Open` and all five non-SQL maps. Was: only `Open::remove_all` ran, so Redis, MongoDB, DynamoDB, Elasticsearch and Cassandra sessions kept the old settings, including the safety level, until they reconnected. TASK-007.

**ISSUE-004 — Two connects racing for one non-SQL session both open, and the second overwrites the first.** Fixed 2026-10-19 in the same commit as ISSUE-003: `Sessions::insert_new` keeps the first session, as `Open::insert_new` does for SQL (`2efec09`), and the five `*_connect` commands use it. Redis still replaces the session when switching database, on purpose. TASK-008.

**ISSUE-005 — Deleting a connection doesn't close its open non-SQL sessions.** Verified by reading code 2026-10-19; not reproduced. `delete_connection` calls only `Open::remove_all`, so a Redis, MongoDB, DynamoDB, Elasticsearch or Cassandra session of a deleted connection stays usable until disconnect. Fix: the same five `remove_all` calls `save_connection` makes. TASK-017.

**ISSUE-010 — Read-only rests on the SQL parse alone for SQL Server, libSQL and D1.** Reported (TODO §24). Their servers can't hold a session read-only, so safe mode's second line (a read-only transaction) is missing, and MCP read-only tokens rely on sqlparser. Also, Postgres lets a function write to temporary tables inside a read-only transaction (TODO §26).

**ISSUE-011 — Oracle LIKE filters are case-sensitive.** Reported (TODO §24): Oracle's LIKE is.

**ISSUE-012 — `cargo deny` warns that `paste` is unmaintained.** Reported 2026-10-18: a build-time macro crate pulled in by `oracle`. A warning, not a vulnerability; `npm run audit` passes.

**ISSUE-013 — MySQL `VERIFY_IDENTITY` through an SSH tunnel checks 127.0.0.1 in backups.** Reported (TODO §14): `mysqldump` gets the tunnel's local address.

**ISSUE-014 — A split pane that isn't focused can't be scrolled or selected.** Reported (TODO §3): the grid and editor draw into one place.

**ISSUE-031 — Bugs found checking the app window, fixed 2026-10-19.** Verified in the app window against `scripts/test-env.sh`, fixed in the same commit: Space never reached the structure editor's fields (the row-detail shortcut took it), so comments and trigger bodies lost their spaces; the document view's filter and sort fields, and the plugin and theme "Install from Git" URL fields, took no typing at all (their `onkeydown` returned `false` for every key but Return); Oracle connections hung in the app until macOS's local network permission was asked for (`Info.plist`); BigQuery failed to open an empty dataset (the emulator has no INFORMATION_SCHEMA there; tables now come from `tables.list`); a metrics table's picked row was unreadable on even rows (the zebra stripes outranked it); new SQL Server triggers started as BEFORE, which SQL Server lacks; ClickHouse's Length column showed `Date` for `Nullable(Date)`; DynamoDB's Region field had another field's placeholder.

**ISSUE-032 — The sidebar doesn't refresh after a CREATE or DROP run in a SQL tab.** Verified 2026-10-19 in the app window (DuckDB, SQL Server, ClickHouse, Oracle, BigQuery, libSQL): the new table shows after ⌘R. Open: whether TablePlus refreshes on its own.

**ISSUE-033 — An Oracle table's grid took 5 to 8 seconds to load the first time.** Seen 2026-10-19 in the app window; not measured. The browse runs the page, a count and a row-identity probe, each a round trip through `spawn_blocking`.

**ISSUE-034 — A date cell's picker took no typed digits from computer-use.** Seen 2026-10-19 on an Oracle DATE column in the app window; not tried by hand, so it may be the automation rather than the app.

**ISSUE-035 — SQL Server's results log shows a `GO` line at the start of later batches' text.** Seen 2026-10-19; cosmetic, the batches run correctly.

## Environment problems

**ISSUE-020 — Shared macOS builds show a Gatekeeper prompt.** Verified 2026-10-15. Ad-hoc signed, not notarized. Workaround in [DEVELOPMENT.md](DEVELOPMENT.md#common-failures). Fix: a Developer ID certificate (paid Apple account).

**ISSUE-021 — Port 1420 clashes.** Reported repeatedly: an agent's Vite preview holds the port `tauri dev` needs (`strictPort`). Stop the preview first.

**ISSUE-022 — cargo not found by `tauri` in some shells.** Reported 2026-10-14: Homebrew rustup isn't on `PATH` in non-login shells. Fixed on the owner's machine via `~/.zshenv`.

## Discrepancies between rules and code

**ISSUE-030 — The async rule says blocking work belongs in `spawn_blocking`, but SQLite and DuckDB use `block_in_place`.** Verified. Deliberate (DEC-024): the connection is borrowed. The rule's wording should be clarified, not the code (Q-003).

## Open questions for the owner

- **Q-001** What comes next after the engine push? The TODO's "Suggested order" is finished; there's no priority among the remaining `[~]` items (TASKS.md).
- **Q-002** Tailwind: adopt it for new components only, or not at all? (DEC-020)
- **Q-003** Should the async rule in CONVENTIONS.md name `block_in_place` as the exception for borrowed connections? (ISSUE-030)
- **Q-004** What was wrong with the first mockingbird logo (reverted in `0820765`)? (DEC-021)
- **Q-005** Delete the old branches (TASK-003)? The main.ts split is merged (TASK-001).
- **Q-006** May agents drive the native app window to verify features, and under what conditions? (DEC-028, TASK-005)
