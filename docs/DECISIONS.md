# Decisions

Significant choices and why they were made. **Basis** says where the evidence is: *owner* (said in a session), *commit* (a commit message states the intent), *code* (a comment in the code), *inferred* (read from the implementation; the reason was never stated). Status is Accepted unless marked. Don't reverse an accepted decision without asking the owner and recording the change here.

## Platform and structure

**DEC-001 — Tauri 2 with a Rust core and a plain TypeScript UI.** 2026-10-13. Basis: owner ("go with Rust, personal tool, scaffold it") for Rust; Tauri and the frameworkless UI were the scaffold's choice, which the owner has built on since. Consequence: the UI is hand-built DOM and CSS (see DEC-020 for Tailwind).

**DEC-002 — TablePlus is the functional reference; the look is our own.** Basis: owner for function ("everything to be same functionality wise", 2026-10-13); commit `36f0704` for the own brand. TODO.md names the TablePlus screen for each feature (`Ref:`).

**DEC-003 — TODO.md is the roadmap and the feature status.** 2026-10-13. Basis: owner ("First create a proper todo list"). Each item says what was tried and against what. These docs summarize it and never duplicate it.

**DEC-004 — Strict Rust rules from three named guides.** 2026-10-13. Basis: owner. Lints in `Cargo.toml`; rules in [CONVENTIONS.md](CONVENTIONS.md).

**DEC-005 — `Conn` holds a `Driver` trait object; SQL differences are `match`es on `Dialect`.** 2026-10-17/18, commits `8d12686` and `ab410c0`. Basis: owner ("Start with the Conn driver trait refactor, the prerequisite for new engines"). Before it, `Conn` was an enum over three engines. Consequence: a new SQL engine is a driver, a dialect and the compiler's list of arms ([features/engines.md](features/engines.md)).

**DEC-006 — Drivers return every cell as text.** Basis: inferred (README: "query (text cells)"; `Driver` returns `Option<String>`). Keeps the UI engine-neutral and avoids JavaScript number precision loss. Consequence: JSON export writes values as strings, and SQLite blobs export as `<n bytes>` (TODO §13).

**DEC-007 — One lock per connection, not one global lock.** Commit `ddee112`, TODO §26. A slow query holds up only its own connection; keep-alive skips busy ones.

**DEC-008 — Safe mode and the passcode lock are enforced in Rust.** Commits `458652a`, `ddee112`, `aa88c2a`. The UI only prompts. At Alert 2 and Safe 2, "read-only" SQL also runs in a read-only transaction, so the database refuses hidden writes ([features/safe-mode.md](features/safe-mode.md)).

**DEC-010 — Engines that aren't SQL get their own session types, in one generic map.** Commits `30f0d49` (Redis), `d164f67` (`sessions::Sessions<S>`). The document stores share one UI view (`nosql/docview.ts`, commit `3ef6c40`).

**DEC-012 — Errors reach the UI as `{ code, message }`; the UI branches on the code.** Commit `2efec09` (2026-10-17 review). Matching on message text broke when wording changed.

**DEC-013 — Strict CSP and `withGlobalTauri: false`.** Commit `2efec09`. Custom scripts still work because they run in a Web Worker, where Tauri doesn't send the CSP (checked in a real WKWebView during merge `f979e28`).

**DEC-014 — Settings live in `settings.json`; localStorage is only a cache.** Commit `a00dd62`. The file wins, so settings can sync between Macs through a shared folder.

**DEC-015 — MCP settings stay in the config folder, never in a synced Location.** Basis: code and TODO §22: access tokens belong to one machine.

**DEC-016 — Prefer writing small things over adding dependencies.** Basis: inferred from repeated choices, several stated in code: a RESP2 client (`nosql/redis.rs`), SigV4 "small enough to keep here rather than pull in the AWS SDK" (`nosql/dynamo/sigv4.rs`), plain SVG charts ("no chart library", TODO §11), the diagram's own layered layout ("no dependency", TODO §16), the updater without `zip` (its advisories).

**DEC-018 — The identifier and keychain services stay `dev.ritesh.dbmanager`.** Basis: code (`connect/store.rs`: "renaming would orphan saved passwords"). Don't "fix" these to TableMinus.

## Engines

**DEC-009 — Oracle uses the `oracle` crate (ODPI-C) and the user's Oracle Instant Client; `oracle-rs` is rejected.** 2026-10-18. Basis: owner ("Switch Oracle to the oracle crate (ODPI-C); users install Oracle Instant Client"). `oracle-rs` 0.1.7 was tried against Oracle 23.26: every SQL error made the server drop the session, and every `ALTER SESSION` reply failed to parse. Patching the handshake didn't help. Accepted because the pure-Rust option didn't work. Don't switch back unless a release fixes error handling. Bundling Instant Client is open (TASK-011).

**DEC-011 — MySQL through the pure-Rust `mysql_async`; no client-library switch.** Basis: TODO §2 ("Not needed: TablePlus's MySQL 'dialect'… the pure-Rust driver talks to old and new servers alike").

**DEC-031 — CockroachDB is a Postgres connection with a flavour.** Commit `504235a`. Everything runs on the Postgres code; differences are found from `version()`.

**DEC-032 — ClickHouse over HTTP, one session per connection.** Commits `9622e14`, `e08b235`. Basis: inferred for HTTP over the native protocol (TODO lists the native protocol as not done). The HTTP session makes `SET` and temporary tables persist.

**DEC-033 — Discovery identifies servers by what they answer, not by port, and never replaces a saved connection.** Commit `6ef4505`; owner request 2026-10-17 (REQ-030).

## Security and distribution

**DEC-019 — macOS bundles are ad-hoc signed as a whole.** 2026-10-15, commit `ce66ccc`. Basis: owner's report that friends' copies said "damaged". Recipients must still use Open Anyway or clear quarantine. Developer ID signing and notarization are wired in (`scripts/build.sh`) but need a paid Apple Developer account, which the owner hasn't set up.

**DEC-022 — Updates come from GitHub Releases' `latest.json`, signed with the owner's key.** Commit `f8c8247`. Beta builds use a release tagged `beta`. Release notes come from `CHANGELOG.md`.

**DEC-023 — `npm run audit` stays out of `npm run check`.** TODO §26: it fetches the advisory database. Run it before releases and dependency changes.

**DEC-030 — On Linux without a Secret Service, secrets go to an encrypted vault file.** Commit `c8c5b2b`. argon2id plus AES-256-GCM, the same sealing as exported connection files.

**DEC-017 — The MCP server is loopback-only.** TODO §20. Status: remote access deferred, "out of scope for now".

## Behaviour that differs from TablePlus on purpose

**DEC-025 — Closing the last item tab keeps the connection open, and ‹ Connections doesn't disconnect.** 2026-10-15. Basis: owner (REQ-025, REQ-026). TablePlus closes the workspace; the earlier code did the same "there on purpose to match TablePlus" and was changed in `8f133b2` and `9105a24`.

**DEC-026 — Workspaces are tabs in one window, not separate windows.** 2026-10-15. Basis: owner allowed either ("it does not need to be a new window at all, it just needs a good system to switch"). Commit `f07097f`.

## Process

**DEC-027 — Test servers run in Docker.** 2026-10-17. Basis: owner ("use docker for test server env to test ssh/remote db"). `scripts/test-env.sh`, `scripts/test-env.yml`.

**DEC-028 — Agents check the UI in a browser with a stubbed backend, not by driving the app window.** Basis: owner denied computer-use access to the app window (sessions of 2026-10-13 and 2026-10-18). On 2026-10-17 the owner did grant Screen Recording and Accessibility for one round of native checks ("you can proceed unsandboxed. But do not change existing database without taking a backup"). Ask each time before driving the native app.

**DEC-024 — SQLite and DuckDB run through `block_in_place`, not `spawn_blocking`.** TODO §26 and `sql/driver.rs`: the connection is borrowed, so it can't move to another thread. This departs from the letter of the async rule in [CONVENTIONS.md](CONVENTIONS.md) ("Blocking work … belongs in `spawn_blocking`"). Open: Q-003 asks whether to amend the rule's wording.

## Proposed or unresolved

**DEC-020 — Tailwind CSS. Proposed: don't adopt.** 2026-10-17. The owner asked "can we use tailwind css on this project?" The agent answered that it's possible (`@tailwindcss/vite`) but advised against it: the DOM is built from TS with dynamic classes, much of the styling is stateful, migration churn, a new dependency. If wanted, add it beside the existing CSS without preflight and use it for new components only. The owner didn't answer. Status: Open (Q-002).

**DEC-021 — The first mockingbird logo was reverted.** 2026-10-16, commit `0820765` ("Back to the four-disc mark… The logo item in TODO.md is open again"), merged through PR #1 (`feature/fixes`). The reason isn't recorded. Status: the logo is still wanted (REQ-063); ask the owner what was wrong with the first one before trying again (Q-004).
