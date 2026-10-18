# TableMinus

A desktop database client for macOS and Linux with TablePlus's features: Tauri 2, a Rust core in `src-tauri/`, a plain TypeScript UI in `src/`.

## Before you work

1. Read [docs/README.md](docs/README.md), the map of the project's documentation and which source wins when they disagree.
2. Read [docs/CONVENTIONS.md](docs/CONVENTIONS.md) before changing code.
3. Resuming earlier work: read [docs/agent-context/HANDOFF.md](docs/agent-context/HANDOFF.md).
4. Before changing a feature, check its entry in [TODO.md](TODO.md) (status and what was tried) and the requirements and decisions in `docs/` that touch it.

## Rules the owner set

- Never push, publish or write to an outside service without explicit permission for that action, asked for just before. Local commits are fine.
- No AI attribution in commits, PRs, comments or files. Commits are authored as `Ritesh Shrestha <shrestha.ritesh@hotmail.com>`.
- `npm run check` must pass before every commit.
- Don't invent requirements, decisions or history. Label what you infer, and ask when it matters.

## After you work

- Update the docs your change affects ([docs/agent-context/WORKFLOW.md](docs/agent-context/WORKFLOW.md) has the table), TODO.md for feature status first.
- If work is left unfinished, update `docs/agent-context/HANDOFF.md`.
