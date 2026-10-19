# Current state

As of 2026-10-18, commit `12be348` on `main`; working tree clean; local `main` is 39 commits ahead of `origin/main` (origin is at `3f34a15`, PR #1; check with `git status -sb`). The detailed, per-feature truth is [../TODO.md](../TODO.md). Update this page when its summary stops being true.

## Levels of evidence

TODO.md uses these, and so should anyone updating it:

1. **In code:** implemented on `main`.
2. **Unit-tested:** covered by `npm run check` (passes: 120 TS tests; 392 Rust tests pass, 71 ignored).
3. **Tried against a real server:** a live test or a manual run against a named server and version, usually from `scripts/test-env.sh`.
4. **Tried in the browser preview:** the UI with a stubbed backend (layout and UI logic only).
5. **Tried in the app window:** the native Tauri window. Many features stop at level 4, because agents may not drive the window (DEC-028).
6. **Tried in a bundled, installed app:** rare. File associations, URL schemes and updates have not been.

Nothing has been released, and no outside user has run a current build.

## By area

TODO.md counts on 2026-10-18: 206 items done `[x]`, 15 partly done `[~]`, 3 not started `[ ]`.

| Area (TODO section) | State |
| --- | --- |
| Launcher, connections, transport (§1–2) | Done. Partial: URL schemes and Finder file opening not tried in a bundled app; SSH ProxyJump/ProxyCommand and host certificates missing |
| Workspace, sidebar, grid, filters, row detail (§3–7) | Done. Partial: an unfocused split pane can't be scrolled or selected without focusing it |
| Structure editing, table operations (§8–9) | Done for Postgres, MySQL/MariaDB, SQLite (with table rebuilds), and to varying depth for the newer engines |
| SQL editor, results, saved queries, history (§10–12) | Done |
| Import/export, backup/restore (§13–14) | Done for the original engines. Not done: Excel export, non-UTF-8 CSV, MySQL `DELIMITER` in SQL import; backup for the newer engines |
| Admin tools, diagram, metrics (§15–17) | Done. The metrics board's widget wiring isn't tried in the app window |
| Safety (§18) | Done ([features/safe-mode.md](features/safe-mode.md)) |
| AI, MCP (§19–20) | Done except GitHub Copilot and remote MCP (deferred) |
| Plugins, scripts, settings, themes (§21–23) | Done; app language is English only |
| Engines (§24) | 17 engines. Done: Redis, DuckDB. Partial: CockroachDB, SQL Server, MongoDB, ClickHouse, libSQL, D1, DynamoDB, Elasticsearch, Cassandra, Oracle, BigQuery (each entry lists what's missing). Not started: Redshift, Greenplum, Vertica, Snowflake |
| App and platform (§25) | Menu bar, title bar, updater, bundles done. Not tried: an update from a real release, Developer ID signing, the Linux bundles on a Linux desktop, the x86_64 build. Logo not started |
| Rust hygiene (§26) | Done |

## Engine depth at a glance

Postgres, MySQL/MariaDB and SQLite are the most complete and the most tested. The others have the grid, editor or console and safe mode, and are missing some of: user management, backup, the structure editor, import/export. Never tried against the real service: Cloudflare D1 (fake server only). Tried against Docker images: everything else in `scripts/test-env.yml`.

## Recent changes (2026-10-17 → 2026-10-19)

- 2026-10-19: Google BigQuery (REST API, service account keys, tried against the emulator); SQL Server named instances, functions, procedures and sequences in the sidebar, and column defaults, comments and triggers in the structure editor.

- The `Driver` trait refactor, then nine engines in two days: DuckDB, SQL Server, ClickHouse, libSQL, D1, MongoDB, DynamoDB, Elasticsearch, Cassandra, Oracle.
- In-app updater; Postgres Unix sockets; wider local discovery; metrics board wiring; the AI proxy's credentials; ⌘T can open an empty tab.
- A project review (2026-10-17) hardened safe mode, connection import and the webview (`2efec09`).
- Linux `.deb` and AppImage build in Docker.

## Not on `main`

| Branch | What | State |
| --- | --- | --- |
| `claude/split-main-ts` (`6302abb`) | `src/main.ts` split into ~17 modules (3,577 → 948 lines) | Based on `f979e28`; `main` has moved 37 commits and `main.ts` grew to 3,853 lines. A trial merge conflicts in `src/main.ts`. Needs redoing (TASK-001) |
| `claude/elated-bartik-7ba84a`, `claude/wonderful-booth-986c41` | Earlier attempts at the same split | Superseded by `claude/split-main-ts` |
| `claude/hungry-poitras-20604c` (`83a091d`, merge `e71a878`) | `npm run test:live`, `docker-compose.yml`, `scripts/test-live.sh`, a CI job for live tests | Not merged. A trial merge into today's `main` has no textual conflicts, but the branch predates `scripts/test-env.*`, so it would add a second compose file (`docker-compose.yml`) next to `scripts/test-env.yml`, and its live tests don't cover the newer engines (TASK-002) |
| `feature/fixes` | Redis and the logo revert | Merged as PR #1 |

Git worktrees for the three `claude/*` branches still exist under `.claude/worktrees/`.

## Needs investigation

- Whether the owner still wants the main.ts split (TASK-001).
- What the owner disliked about the first mockingbird logo (Q-004).
- `src-tauri/Cargo.toml`'s `description` and the README's Layout table describe the three-engine app (ISSUE-001).
