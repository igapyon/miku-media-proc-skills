# Decisions

## 2026-07-05 - Record Reusable Media-Processing Decisions

- Problem: The repository had a `DECISIONS.md` rule inside the skill references, but the project root did not yet have a decision log and `README.md` did not mention the rule.
- Background: `miku-media-proc` already requires solved media-processing problems to be recorded in the project root `DECISIONS.md`.
- Decision: Keep `DECISIONS.md` as the repository-level operational decision log from this point forward.
- Resolution: Create `DECISIONS.md` at the project root and document the rule in `README.md`.
- Verification: Checked that no root `DECISIONS.md` existed before creating this file.
- Reuse: When a media-processing problem is diagnosed and solved in a reusable way, append a concise entry here using the skill's decision-log format.
