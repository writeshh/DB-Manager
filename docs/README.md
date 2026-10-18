# TableMinus documentation

The project's persistent memory: what TableMinus is, why it is built the way it is, what exists, and what to do next. It is written so that any agent (Claude Code, Codex, Cursor, Copilot…) or person can pick the project up without the conversations it came from.

Last full audit: 2026-10-18, at commit `12be348` on `main`.

## Reading order for a new agent

1. [PROJECT.md](PROJECT.md): what this is and what the owner wants. Two minutes.
2. [agent-context/WORKFLOW.md](agent-context/WORKFLOW.md): how to work here (commits, pushing, checks, what not to assume).
3. [CONVENTIONS.md](CONVENTIONS.md): the coding rules. Required before touching code.
4. [agent-context/HANDOFF.md](agent-context/HANDOFF.md): when continuing earlier work.
5. Then only what the task needs, from the map below.

Don't read every file for every task. Follow the links from the area you're changing.

## Map

| Question | Document |
| --- | --- |
| What is it, for whom, goals and non-goals | [PROJECT.md](PROJECT.md) |
| What must it do (stable IDs `REQ-…`) | [REQUIREMENTS.md](REQUIREMENTS.md) |
| Feature-by-feature status, roadmap | [../TODO.md](../TODO.md) (canonical) and the summary in [CURRENT-STATE.md](CURRENT-STATE.md) |
| How the code fits together | [ARCHITECTURE.md](ARCHITECTURE.md) |
| What is stored where | [DATA-MODEL.md](DATA-MODEL.md) |
| Libraries, versions, why | [TECH-STACK.md](TECH-STACK.md) |
| Databases, AI providers, tools, services it talks to | [INTEGRATIONS.md](INTEGRATIONS.md) |
| Build, run, test, release | [DEVELOPMENT.md](DEVELOPMENT.md) |
| Coding rules | [CONVENTIONS.md](CONVENTIONS.md) |
| Look and feel, the owner's UI feedback | [UI-UX.md](UI-UX.md) |
| Why things are as they are, rejected approaches (`DEC-…`) | [DECISIONS.md](DECISIONS.md) |
| What to work on (`TASK-…`) | [TASKS.md](TASKS.md) |
| Bugs, limits, open questions (`ISSUE-…`, `Q-…`) | [KNOWN-ISSUES.md](KNOWN-ISSUES.md) |
| How the project evolved | [HISTORY.md](HISTORY.md) |
| Deep dives: safe mode, engines, MCP and AI | [features/](features/README.md) |
| Working state of the current task | [agent-context/HANDOFF.md](agent-context/HANDOFF.md) |
| What past sessions did | [agent-context/sessions/README.md](agent-context/sessions/README.md) |

Files outside `docs/` that stay where they are, because tools or the app read them:

- [../TODO.md](../TODO.md): the TablePlus-parity roadmap with `[x]`/`[~]`/`[ ]` status and per-feature notes on what was tried. It is the canonical feature status; these docs summarize it and never copy it.
- [../CHANGELOG.md](../CHANGELOG.md): user-facing release notes. The app shows them (Help › Release Notes) and `scripts/latest-json.mjs` copies a version's section into the update feed, so its format matters. The developer-facing history is [HISTORY.md](HISTORY.md).
- [../README.md](../README.md): the GitHub front page.
- [../AGENTS.md](../AGENTS.md) and [../CLAUDE.md](../CLAUDE.md): entry points that tools load automatically. They point here.

## Which source wins

1. The owner's latest explicit instruction in the current conversation.
2. Confirmed requirements ([REQUIREMENTS.md](REQUIREMENTS.md)) and accepted decisions ([DECISIONS.md](DECISIONS.md)).
3. The code on `main`: what is actually implemented.
4. Tests and recorded runs (TODO.md's "Tried against…" notes): evidence that it works.
5. Past conversations ([agent-context/sessions/](agent-context/sessions/README.md)): context and reasons, overridden by anything newer.
6. Anything labelled *inferred*, *proposed* or *open*: provisional until the owner confirms.

When a document and the code disagree, the code says what *is* and the document may say what was *intended*. Don't silently edit either to match the other: record the gap in [KNOWN-ISSUES.md](KNOWN-ISSUES.md) and ask when it matters.

Labels used throughout: **Confirmed** (the owner said so, or a commit message states it as intent), **Inferred** (read from code or history, not stated by the owner), **Open** (unresolved).

## Keeping it current

- Update only the documents your change affects, in the same commit as the change. [agent-context/WORKFLOW.md](agent-context/WORKFLOW.md) lists which document goes with which kind of change.
- Feature status goes in TODO.md first; touch [CURRENT-STATE.md](CURRENT-STATE.md) only when its summary would become wrong.
- Keep each fact in one place and link to it.
- Stable IDs (`REQ-`, `DEC-`, `TASK-`, `ISSUE-`, `Q-`) are never reused. Mark retired ones as superseded or done instead of deleting them.
- Never put secrets in docs: name the variable or keychain entry, not its value.
