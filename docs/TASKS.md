# Tasks

Known, actionable work, as of 2026-10-19. Feature gaps live in [../TODO.md](../TODO.md); this list holds work TODO.md doesn't track (branches, process, fixes found in review) and points into TODO.md for the rest. **Confirmed** means the owner asked for it; **Proposed** means an agent suggests it and the owner hasn't decided.

**The standing direction** (owner, 2026-10-18): "continue with all the leftover todos". The owner has set no priority among the remaining TODO.md items since TODO's "Suggested order" was finished. Ask before starting anything large, and say which item you're picking.

## Needs the owner first

| ID | Task | Why it matters | Status | Blocked on |
| --- | --- | --- | --- | --- |
| TASK-001 | Redo the `src/main.ts` split on top of today's `main` | Done 2026-10-19: `claude/split-main-ts` merged, with the 37 commits of `main.ts` changes since `f979e28` ported into the modules that now hold that code | Done | |
| TASK-002 | Land the live-test runner from `claude/hungry-poitras-20604c` | Done 2026-10-19 (not the branch itself): `npm run test:live`, with its MySQL, Postgres and Redis servers folded into `scripts/test-env.yml` under the `live` profile and a CI job. It skips the tests for the engines that need `scripts/test-env.sh up` | Done, CI job not run | |
| TASK-003 | Remove the old branches (`claude/elated-bartik-7ba84a`, `claude/wonderful-booth-986c41`, `claude/hungry-poitras-20604c`, `claude/split-main-ts`, `feature/charming-mestorf-7f41f0`) | The worktrees are gone (2026-10-19) and nothing on the branches is unmerged or unported: the first two are earlier attempts at the split, the others are merged or redone | Proposed | Owner's OK: deleting branches is hard to undo |
| TASK-004 | Publish the first release | The updater has never run against a real release. Steps in [DEVELOPMENT.md](DEVELOPMENT.md#release). Version is still 0.1.0, with a large Unreleased changelog section | Proposed | Owner's explicit go-ahead to push and publish (REQ-091) |
| TASK-005 | Verify the features marked "not tried in the app window" | The owner asked on 2026-10-17 ("verify the untried features") and 2026-10-18 ("check the DuckDB, SQL Server and ClickHouse forms and grids in the window") | Confirmed, mostly done | Done 2026-10-19 in the app window (the owner allowed it): every engine's form and connection, DuckDB, SQL Server, ClickHouse, Oracle, libSQL grids and edits, the document view engines, Find Local Databases, the metrics board's moving and wiring, the AI proxy fields, ⌘T's empty tab, split panes, dragging by the tab strip. Left: the Needs credentials badge, the metrics board's auto-refresh, lock, resize, boards and import/export, anything needing a bundled or signed app. Findings: ISSUE-031 to ISSUE-035 |
| TASK-006 | New logo, a blue mockingbird (REQ-063, TODO §25) | Still wanted | Confirmed | Q-004: what was wrong with the reverted one |

## Ready to do

| ID | Task | Why it matters | Files |
| --- | --- | --- | --- |
| TASK-009 | Update `src-tauri/Cargo.toml`'s `description` (ISSUE-001) | It still says "Postgres, MySQL/MariaDB and SQLite" | `src-tauri/Cargo.toml` |
| TASK-017 | Deleting a connection should also drop its open non-SQL sessions (ISSUE-005) | A deleted Redis, MongoDB, DynamoDB, Elasticsearch or Cassandra connection's session stays usable until disconnect | `src-tauri/src/lib.rs` (`delete_connection`; `save_connection` shows the five `remove_all` calls) |

## From TODO.md (not prioritized by the owner)

Pointers only; TODO.md has the detail.

- TASK-010: the 15 `[~]` items, mostly engines' "Not done" lists (§24). Examples: SQL Server's Entra ID sign-in and user management, MongoDB import/export and structure view, Oracle packages and users, Cassandra structure view, DynamoDB table creation.
- TASK-011: bundle Oracle Instant Client with the app, or keep it a user install (§24 Oracle, DEC-009).
- TASK-012: Snowflake; Redshift, Greenplum, Vertica (§24). None has a Docker image to test against. (BigQuery done 2026-10-19.)
- TASK-013: run the Linux `.deb` and AppImage on a Linux desktop; build x86_64 (§25).
- TASK-014: SSH ProxyJump/ProxyCommand and host certificates; a key file picker (§2).
- TASK-015: MySQL roles, MariaDB lock and expiry in user management (§15).
- TASK-016: GitHub Copilot as an assistant provider (§19).

## Done recently (for context)

- BigQuery (`082a2e1`) and SQL Server's sidebar and structure-editor gaps, 2026-10-19; `npm run check` passed and the ignored live tests ran against the emulator and SQL Server 2022.

- Oracle on the `oracle` crate with Instant Client: committed as `12be348` on 2026-10-18; `npm run check` and `npm run audit` passed.
- This documentation system: committed 2026-10-19 (see [agent-context/HANDOFF.md](agent-context/HANDOFF.md)).
- TASK-007 and TASK-008 (ISSUE-003, ISSUE-004): saving a connection drops its non-SQL sessions too, and non-SQL connects keep the first session when two race. Committed 2026-10-19 on `feature/charming-mestorf-7f41f0`, already in `main` as `f2dacb4`. ISSUE-005 (the same gap in `delete_connection`) is still open.
