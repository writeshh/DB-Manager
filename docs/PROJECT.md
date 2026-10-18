# Project

## What it is

TableMinus is a desktop database client for macOS and Linux, built as a feature-for-feature counterpart of TablePlus (version 27.0.2 as the reference). One window with a launcher of saved connections and tabbed workspaces: browse and edit table data, edit structure, write and run SQL, import and export, back up and restore, and administer users and sessions, across 16 engines (see [CURRENT-STATE.md](CURRENT-STATE.md)).

- Repository: `github.com/writeshh/TableMinus` (moved from `writeshh/Table-Minus` on 2026-10-14).
- Stack: Tauri 2, a Rust core in `src-tauri/`, a plain TypeScript UI in `src/` without a framework ([TECH-STACK.md](TECH-STACK.md)).
- Owner and only developer: Ritesh Shrestha. Most code so far was written by AI agents under the owner's direction.

## Origin (confirmed from session history)

1. 2026-10-12: the owner had an agent reverse-engineer `/Applications/TablePlus.app` (REA with Ghidra) and write up an internals report, published as a private claude.ai artifact.
2. 2026-10-13: asked "what do we need to create a desktop app (linux and mac) based on the findings?", then **"go with Rust, personal tool, scaffold it"**.
3. Same day: **"First create a proper todo list. Take TablePlus as reference. I want everything to be same functionality wise. So feel free to browse and use table plus."** That list is [../TODO.md](../TODO.md).
4. Also that day: follow three named Rust best-practice guides ([CONVENTIONS.md](CONVENTIONS.md)).

## Who it's for

- **Confirmed:** a personal tool for the owner's own work.
- **Confirmed:** the owner has shared earlier builds with "a couple of friends" on other Macs (2026-10-15), so a bundle that installs on someone else's Mac matters ([DECISIONS.md](DECISIONS.md) DEC-019).
- **Inferred:** the in-app updater from GitHub Releases, the changelog and signing support point at distributing builds beyond the owner's machine. No release has been published yet.

## Goals

- **Functional parity with TablePlus** (REQ-001): everything it does with databases, screen by screen. TODO.md names the TablePlus screen to study for each item (`Ref:`).
- **macOS and Linux** (REQ-002). Windows is not a goal; some code keeps it building (`cmd /C` for startup scripts) but nothing is tested there.
- **Safe with real data** (REQ-010 to REQ-016): passwords only in the keychain, safe mode enforced in the backend, no data lost to a failed write.
- **Strict Rust quality bar** ([CONVENTIONS.md](CONVENTIONS.md)): strict lints, typed errors, bound SQL parameters, `npm run check` passing before every commit.

## Non-goals

- **Confirmed** (TODO.md "Not copying"): TablePlus's licensing, trial limits, purchase and register screens, the iOS promo, Sentry crash reporting, and the "report bug" form. They belong to TablePlus as a product.
- **Confirmed** (TODO.md §20): a remotely reachable MCP server; the server stays on loopback "for now".
- **Inferred:** no cloud service or account of its own; everything is local files plus the OS keychain.

## Principles visible in the work

- Copy TablePlus's behaviour, not its looks: the visual design is the project's own brand ([UI-UX.md](UI-UX.md)).
- Prefer fewer dependencies: charts are plain SVG, the diagram layout is hand-written, DynamoDB signing is a small module rather than the AWS SDK ([DECISIONS.md](DECISIONS.md)).
- Record honestly what was tried and what wasn't: TODO.md says "Tried against …" or "Not tried in the app window" for each feature. Keep doing this.

## Names

The app was scaffolded as "DB Manager" (identifier `dev.ritesh.dbmanager`), renamed "Table Minus" on 2026-10-14 (a parallel session tried "Table Zero"; not kept), then spelled **TableMinus** everywhere (commit `2961138`). The identifier and the keychain service name stay `dev.ritesh.dbmanager` on purpose (DEC-018).
