# miku-media-proc-skills

Repository-local Codex skill package for media processing workflows.

## Repository Layout

- `skills/miku-media-proc/`: canonical source for the `miku-media-proc` skill.
- `workplace/`: local scratch area for generated files, copied materials, and verification artifacts.

## Operational Decisions

- Record reusable media-processing problems, decisions, and resolutions in the project root `DECISIONS.md`.
- Create `DECISIONS.md` if it does not exist, or append a new concise entry if it already exists.
- Do not record routine steps that did not involve a problem, tradeoff, or reusable decision.
- Follow the entry format in `skills/miku-media-proc/references/integration/decisions-log.md`.

## Git Rules

- Commit skill source files under `skills/`.
- Do not commit local deployment copies under `.codex/skills/`.
- Do not commit `workplace/` contents except `workplace/.gitkeep`.
- Do not commit local editor MCP settings such as `.vscode/mcp.json`.
- macOS `.DS_Store` files are ignored.
