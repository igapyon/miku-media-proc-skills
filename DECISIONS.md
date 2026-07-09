# Decisions

## 2026-07-05 - Record Reusable Media-Processing Decisions

- Problem: The repository had a `DECISIONS.md` rule inside the skill references, but the project root did not yet have a decision log and `README.md` did not mention the rule.
- Background: `miku-media-proc` already requires solved media-processing problems to be recorded in the project root `DECISIONS.md`.
- Decision: Keep `DECISIONS.md` as the repository-level operational decision log from this point forward.
- Resolution: Create `DECISIONS.md` at the project root and document the rule in `README.md`.
- Verification: Checked that no root `DECISIONS.md` existed before creating this file.
- Reuse: When a media-processing problem is diagnosed and solved in a reusable way, append a concise entry here using the skill's decision-log format.

## 2026-07-08 - Prefer ffprobe then Resolve Studio Python API for Timeline Assembly

- Problem: A real Resolve assembly workflow showed that the skill did not clearly state the default order for media inspection, Resolve project creation, API automation, frame-rate handling, narration API checks, and playback verification.
- Background: Source media was inspected with `ffprobe`, then a Resolve project was created with API automation. The source was `1280x720 / 30fps`, assembled into timeline `assemble_001` in project `codex_20260708_obs_source_fps`, while `timelinePlaybackFrameRate` still reported `24`.
- Decision: Use `ffprobe` as the default metadata inspection tool, guide timeline assembly/export work toward DaVinci Resolve Studio by default, treat human-launched Resolve plus Python Resolve API automation as the standard direct automation path, and report unresolved Resolve playback-frame-rate mismatches instead of repeatedly forcing API settings.
- Resolution: Update the skill workflow, Resolve notes, frame-rate policy, Zundamon notes, and quality-check guidance to reflect the operational order and tool choices.
- Verification: Documentation-only update; no media processing command was run for this repository change.
- Reuse: For new Resolve projects, inspect sources first, set timeline fps before timeline creation, assemble in a fresh project or timeline when fps must change, and use VLC as the primary playback check for generated audio/video.
