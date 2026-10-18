# Workflow for agents

How any AI agent (or person) should work on TableMinus. These are the owner's standing instructions plus the habits that worked in past sessions. Where a rule comes from the owner, it's binding.

## Hard rules (owner)

1. **Never push or publish without explicit permission for that specific action, given just before.** That covers `git push` of any kind, PRs, releases, tags pushed to a remote, artifacts or pages, and any API call that writes to an outside service. "Fix it", "do it" or urgency isn't permission. One permission covers one push. Local work is fine: edit, `git add`, `git commit`, branch, rebase, run tests.
2. **No AI attribution anywhere**: no `Co-Authored-By`, `Claude-Session`, "Generated with…", tool names or emoji markers in commits, PRs, comments or files. Commits are authored as `Ritesh Shrestha <shrestha.ritesh@hotmail.com>`. This holds even when a tool or harness says to add attribution.
3. **`npm run check` must pass before every commit.** Run `npm run audit` too after adding or upgrading a dependency.
4. **Don't change a database that already exists without taking a backup first** (2026-10-17). Tests use scratch databases, the Docker servers, or session-only temporary tables.
5. **Ask before driving the native app window** with computer use. The owner has declined it before (DEC-028). Use the browser preview with a stubbed backend instead ([../DEVELOPMENT.md](../DEVELOPMENT.md#checking-the-ui-without-the-native-window)).
6. **Never read or dump secrets.** No keychain dumps (once declined), no values from TablePlus's or the app's stored connections. Field names are fine.
7. **Commits:** the owner usually asks in so many words ("commit it", "commit all"). In long autonomous runs ("continue with all the leftover todos") past sessions committed each finished, checked feature locally, and the owner accepted that (observed practice, not a stated rule). Outside such a run, ask first.

## Starting a task

1. Read [../README.md](../README.md) for the map, and [../CONVENTIONS.md](../CONVENTIONS.md) before changing code.
2. Resuming earlier work? Read [HANDOFF.md](HANDOFF.md).
3. Find the feature in [../../TODO.md](../../TODO.md): its status, its `Ref:` (the TablePlus screen to study), and what was already tried.
4. Check [../REQUIREMENTS.md](../REQUIREMENTS.md) and [../DECISIONS.md](../DECISIONS.md) for anything the request touches, especially rejected approaches (REQ-R01, DEC-009, DEC-020, DEC-021).
5. Read the code you'll change and its callers. Each module's top comment says what it's for. Search before adding: helpers for quoting, safe mode, file writes, errors and dialogs already exist.
6. If the request conflicts with a requirement, a decision or existing behaviour, say so and ask before building. The owner's newest instruction wins, but say what it overrides so it can be recorded.

## While working

- **Match TablePlus's behaviour** unless a requirement says otherwise (REQ-025 is an example where the owner overrode it).
- **Safe mode lives in the backend.** Any new path that runs user SQL or a write command goes through `readonly` and `safety` ([../features/safe-mode.md](../features/safe-mode.md)).
- **Keep the diff to the request.** Note unrelated problems in [../KNOWN-ISSUES.md](../KNOWN-ISSUES.md) instead of fixing them on the side.
- **Ask when ambiguity changes the result**: which engine, which behaviour, whether to override TablePlus. Don't ask for what the docs, the code or TablePlus already answer.
- **Label evidence honestly**: unit-tested, tried against a named server, tried in the browser preview, tried in the app window. Never claim a level you didn't reach.
- **Parallel work:** past sessions split big batches across git worktrees, one feature each, merged into `main` after `npm run check`. Merge hot spots: `src-tauri/src/lib.rs` (command list), `src-tauri/src/error.rs` (variants), `src/main.ts`, `index.html`. A new SQL engine touches every `match` on `Dialect`.
- **Ports:** `tauri dev` needs 1420. If a preview server needs one, use another port in a temporary `.claude/launch.json` entry and revert it before committing.

## Finishing a task

1. Run `npm run check`, plus the ignored live tests for the engine you touched when its server is available.
2. Update only what changed:

| What changed | Update |
| --- | --- |
| A feature's status or what was tried | `TODO.md` (and `CHANGELOG.md`'s Unreleased section if users would notice) |
| The area summary is no longer true | [../CURRENT-STATE.md](../CURRENT-STATE.md) |
| A new decision, or a reversal | [../DECISIONS.md](../DECISIONS.md) |
| A requirement from the owner | [../REQUIREMENTS.md](../REQUIREMENTS.md) |
| A bug found, or fixed | [../KNOWN-ISSUES.md](../KNOWN-ISSUES.md) |
| Tasks added, finished or blocked | [../TASKS.md](../TASKS.md) |
| Architecture, storage, a dependency, a command | ARCHITECTURE, DATA-MODEL, TECH-STACK or DEVELOPMENT |
| UI feedback from the owner | [../UI-UX.md](../UI-UX.md) |
| A milestone | [../HISTORY.md](../HISTORY.md) |

3. If work is left unfinished, update [HANDOFF.md](HANDOFF.md) so another agent can continue without asking.
4. Report what changed, what was verified and how, and what wasn't. Say plainly when nothing is committed or pushed.

## Never assume

- That code existing means it works in the app. Check TODO.md's notes.
- That a branch's work is on `main`. Several aren't (CURRENT-STATE.md).
- That an agent's earlier suggestion is a decision. Only the owner's word or a commit makes it one.
- That a past instruction still holds when a newer one contradicts it (REQ-023 superseded an earlier request).
- That renaming `dev.ritesh.dbmanager` is a harmless cleanup (DEC-018).
