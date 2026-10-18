@AGENTS.md

@docs/CONVENTIONS.md

## Claude Code specifics

- The coding rules that used to be in this file are in `docs/CONVENTIONS.md`, imported above so they still load at the start of every session.
- Use the auto-memory folder only for facts about this machine or session tooling. Project knowledge goes in `docs/`, where other tools can read it.
- To check the UI, use the `vite` entry in `.claude/launch.json` with a stubbed backend ([docs/DEVELOPMENT.md](docs/DEVELOPMENT.md#checking-the-ui-without-the-native-window)). Ask before driving the native app window with computer use.
