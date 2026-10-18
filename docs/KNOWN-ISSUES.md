# Known issues

Bugs, limitations and open questions, as of 2026-10-19. **Verified** means reproduced or read in the code at `12be348`; **Reported** means from a past session or TODO.md and not rechecked. Engine and feature limitations that TODO.md already lists under "Not done" aren't repeated here. Read TODO.md §24 for those.

## Bugs and gaps

**ISSUE-001 — Stale descriptions of the three-engine app.** Verified. `src-tauri/Cargo.toml` `description` says "Postgres, MySQL/MariaDB and SQLite". The README's engine line and Layout table said the same until this documentation pass updated them. Fix: TASK-009.

**ISSUE-002 — `src/main.ts` is 3,853 lines.** Verified. Every UI change touches it, so parallel branches conflict there (the reason the first split was held back, per the 2026-10-14 workspace-window notes). See TASK-001.

**ISSUE-003 — Saving a connection doesn't close its open non-SQL sessions.** Verified by reading code; not reproduced. `save_connection` calls `Open::remove_all`, which drops only SQL sessions. Redis, MongoDB, DynamoDB, Elasticsearch and Cassandra sessions keep the old settings, including the safety level, until they reconnect. `disconnect` does remove all five. Expected: the same as SQL. First noted in the 2026-10-17 review for Redis. TASK-007.

**ISSUE-004 — Two connects racing for one non-SQL session both open, and the second overwrites the first.** Verified by reading code. `sessions::Sessions::insert` replaces; the SQL side uses `Open::insert_new`, which keeps the first (fixed for SQL in `2efec09`). Impact: one extra connection, closed when dropped. TASK-008.

**ISSUE-010 — Read-only rests on the SQL parse alone for SQL Server, libSQL and D1.** Reported (TODO §24). Their servers can't hold a session read-only, so safe mode's second line (a read-only transaction) is missing, and MCP read-only tokens rely on sqlparser. Also, Postgres lets a function write to temporary tables inside a read-only transaction (TODO §26).

**ISSUE-011 — Oracle LIKE filters are case-sensitive.** Reported (TODO §24): Oracle's LIKE is.

**ISSUE-012 — `cargo deny` warns that `paste` is unmaintained.** Reported 2026-10-18: a build-time macro crate pulled in by `oracle`. A warning, not a vulnerability; `npm run audit` passes.

**ISSUE-013 — MySQL `VERIFY_IDENTITY` through an SSH tunnel checks 127.0.0.1 in backups.** Reported (TODO §14): `mysqldump` gets the tunnel's local address.

**ISSUE-014 — A split pane that isn't focused can't be scrolled or selected.** Reported (TODO §3): the grid and editor draw into one place.

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
- **Q-005** Redo the main.ts split (TASK-001), and keep or delete the old split branches (TASK-003)?
- **Q-006** May agents drive the native app window to verify features, and under what conditions? (DEC-028, TASK-005)
