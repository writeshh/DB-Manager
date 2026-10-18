# History

How the project got here, for developers. Not every commit: `git log` has those, and [../CHANGELOG.md](../CHANGELOG.md) has the user-facing release notes. Dates are commit dates. Add an entry when a change affects architecture, requirements or how people work here.

## 2026-10-12 — Research

The owner had an agent reverse-engineer TablePlus 27.0.2 (REA, Ghidra) and write an internals report as a private claude.ai artifact. TODO.md later drew on it and on the app bundle's menus, settings and screen labels.

## 2026-10-13 — Scaffold and core

- `1676610` Tauri app with Postgres, MySQL and SQLite as one `Conn` enum. Launcher, database picker, settings, tabbed workspace, CodeMirror, inline cell editing.
- `9b9f229` The Rust rules and TODO.md, the TablePlus-parity roadmap.
- SSH tunnels, TLS modes, startup commands, reconnect, query timeout, filters, sorting, copy, run current statement, formatting, parameters, `.sql` files.
- First parallel run: import/export, structure editing, backup/restore and the MCP server, each in its own git worktree, merged into an `integration` branch.

## 2026-10-14 — Workspace and parity sweep

- Renamed to "Table Minus" (`2047938`); a parallel attempt at "Table Zero" wasn't kept. Repository moved to `writeshh/TableMinus`.
- `ddee112` One lock per connection; the passcode lock moves into the backend.
- Second parallel run: grid editing, safety levels and themes, workspace navigation and the native menu bar, launcher, database tabs, results, diagram, users, metrics. All merged.
- `03e159a` `npm run bundle` (`scripts/build.sh`).

## 2026-10-15 — Polish and the long tail

- Spelled TableMinus everywhere (`2961138`). Virtualized grid, paging and estimates, foreign-key navigation, editing tables without a primary key.
- `f07097f` Workspace tabs across the top (REQ-022, REQ-024). `a00dd62` Settings in `settings.json`.
- `36f0704` The brand restyle: ink, paper and four primaries. `ce66ccc` Ad-hoc signing for shared builds.
- The owner's UI feedback list (UI-UX.md) and its fixes; resizable panes; SQL tabs kept per database.
- SQLite table rebuilds, triggers, checks, MCP one-click setup, the AI assistant (providers, then ACP agents), Linux vault, custom title bar, plugins, custom scripts, Git themes, Redis.
- `f94ac7b` A mockingbird logo, reverted the next day in `0820765`.

## 2026-10-16 — First PR

PR #1 (`feature/fixes`: Redis and the logo revert) merged on GitHub as `3f34a15`. `origin/main` hasn't moved since.

## 2026-10-17 — Review and hardening

- The owner asked for a project review, then "fix the issues on your findings": `2efec09` hardened safe mode, connection import and the webview (strict CSP, error codes, connect races), and added CI.
- `f979e28` Merged origin's 45 commits into local main. The main.ts split was redone on `claude/split-main-ts` and not merged. A live-tests branch was left unmerged too (CURRENT-STATE.md).
- Launcher and title-bar polish from the owner's feedback; greyed-out menu items; native checks run with Screen Recording and Accessibility granted.
- `3df5abd` Docker test servers (`scripts/test-env.sh`), which found and fixed Postgres over SSH.

## 2026-10-18 — Engines

- `504235a` CockroachDB as a Postgres flavour; `6ef4505` Find Local Databases.
- `ab410c0` **`Conn` on a `Driver` trait** (DEC-005), then DuckDB, SQL Server, ClickHouse, libSQL, D1, MongoDB, DynamoDB, Elasticsearch, Cassandra and Oracle. `d164f67` one generic session map for the non-SQL engines.
- `f8c8247` In-app updater from GitHub Releases. `4442089` Linux bundles build.
- `12be348` Oracle through the `oracle` crate and Instant Client, after the pure-Rust `oracle-rs` failed (DEC-009).

## 2026-10-19 — Documentation

`docs/` created as the project's persistent context; `AGENTS.md` added; the coding rules moved from `CLAUDE.md` to `docs/CONVENTIONS.md`.
