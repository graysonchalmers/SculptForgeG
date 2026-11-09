# Driving SculptForge from an AI agent

Three ways in, from most to least headless. Pick the lowest one that does the job.

| Need | Use | Window? |
|---|---|---|
| Any command: sculpt, paint, merge, export | `sculptforge-cli -c "<cmds>"`, `-f script.txt`, or `--mcp` (stdio MCP server, one tool per command) | No |
| Screenshot of a panel, or a click path | `sculptforge --ui-test file.sfui --ui-out DIR` | Yes (runs a real window, scripted) |
| Work inside the user's open window | `sculptforge-cli --mcp --attach` | Yes (needs Preferences > AI > "Let AI tools control this window" and one Approve click) |

Claude Code: `claude mcp add sculptforge -- sculptforge-cli --mcp`

## Screenshots of panels (`--ui-test`)

Synthetic OS clicks are ignored by the app. `--ui-test` drives the UI from inside, so it works on Windows
without focus tricks. Settings are isolated for the run, so it does not touch the user's preferences.

```
sculptforge --ui-test shot_panel.sfui --ui-out out\shots
```

Recipe (`tests/ui/ai_recipe_shot.sfui`): open a project, show a panel, take a shot, check a value.

```
run open path=%PROJECT%
click "Tool/Parts"
shot parts-panel
expect_value ...
```

Verbs: `run <cli command>`, `click "<label>"`, `stroke`, `key`, `wait_for`, `shot <name>`, `expect_text`,
`expect_value <name> == N`. See `tests/ui/*.sfui` (hundreds of examples) and `docs/testing-flow.md`.

## Known limits

- Live mode is a consent gate on purpose. Do not try to bypass it; use the CLI or `--ui-test` instead.
- Welcome and tuning pop-ups can intercept the first click of a scripted launch; start the script with
  the same `run` lines the tests use, or use the build that removes them.
