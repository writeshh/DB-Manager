# TableMinus

Tauri 2 app: Rust core in `src-tauri/`, plain TypeScript UI in `src/`. The feature roadmap (TablePlus parity) is [TODO.md](TODO.md).

Before every commit run `npm run check` (tsc, rustfmt, clippy with warnings as errors, tests). It must pass.

No Claude or AI attribution anywhere: no `Co-Authored-By`, `Claude-Session` or "Generated with Claude Code" lines in commits, PRs, comments or files. Commits are authored as `Ritesh Shrestha <shrestha.ritesh@hotmail.com>`, never as Claude.

## Rust rules

Sources: [Apollo Rust best practices](https://github.com/apollographql/rust-best-practices), [Corgea Rust security](https://corgea.com/learn/rust-security-best-practices), [kobogithub rust-best-practices](https://github.com/kobogithub/knowledge/tree/prod/skills/rust-best-practices). The lints in `Cargo.toml` enforce what they can; the rest is on review.

**Lints**
- `unsafe_code` is forbidden. `clippy::all` is denied; `pedantic`, `unwrap_used`, `expect_used` and `panic` are enforced. Tests may unwrap (`clippy.toml`).
- Fix a warning rather than silencing it. When a lint is wrong for one spot, use `#[expect(lint, reason = "...")]` on that item, never a crate-wide `allow`.

**Errors**
- Every fallible function returns `crate::error::Result`. Add a variant to `error::Error` (thiserror) rather than passing strings around; it reaches the UI as its message.
- No `unwrap`/`expect`/`panic!` outside tests. Use `?`, `let ... else`, `ok_or`, `unwrap_or_default`.
- `let _ =` on a `Result` needs a comment saying why the error doesn't matter.
- Never lose user data on an error: refuse to write over a file that failed to load, and write files to a temporary path and then rename.

**Input and SQL**
- Values go into SQL as bound parameters (`$n` / `?`; `PgText` on Postgres). Only two things are escaped by hand: identifiers, through `Conn::ident` (`Dialect::ident`), and string literals in DDL, which takes no parameters (comments), through `Dialect::literal`.
- Fixed value sets are enums (`Engine`), not strings or booleans. Types the UI sends are `#[serde(deny_unknown_fields)]`.
- Cap anything untrusted that drives an allocation or a loop (row counts, file sizes, import lengths).

**Secrets**
- Passwords and URLs holding passwords are `secrecy::SecretString`. Call `expose_secret()` only at the point of use. Never `Debug`, log or persist them; the keychain is the only store.

**Async**
- Every network call has a timeout (`CONNECT_TIMEOUT` for connects).
- Never hold a `std::sync::Mutex` guard across `.await`. Blocking work (SQLite, file I/O on large files, external tools) belongs in `spawn_blocking`.
- External tools (`pg_dump` etc.): `Command::arg` per argument, never `sh -c`; passwords via environment variables, never arguments.

**Style**
- Borrow in signatures (`&str`, `&[T]`, `&Path`); clone only when ownership is really needed.
- Comments say why, not what. Doc comments (`///`) on items whose purpose isn't obvious from the name; `//!` at the top of each module.
- Extract a function when the name adds meaning or the same decision appears three times, not to save lines.
- Tests: one behavior per test, named for the unit and what it should do (`save_refuses_to_overwrite_an_unreadable_file`), covering error paths too. Keep each test's action and assertion inline.

**Release**
- `overflow-checks = true` and `panic = "unwind"` stay in the release profile.
- Before a release, and after adding or upgrading a dependency: `npm run audit` (`cargo deny check` and `cargo audit`; install them with `cargo install --locked cargo-deny cargo-audit`). An ignored advisory needs its reason in `src-tauri/deny.toml`.
