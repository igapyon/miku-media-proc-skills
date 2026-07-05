# Decisions Log

Use this reference when a media-processing problem is diagnosed and solved while
using `miku-media-proc`.

## When To Record

Record a decision when the work discovers a reusable lesson, for example:

- a video conversion failed and a working FFmpeg command or codec choice solved
  it;
- DaVinci Resolve rejected an API setting and a workaround was chosen;
- audio/video sync, frame rate, rotation, sample rate, or timeline placement was
  diagnosed and fixed;
- a YouTube delivery range or export setting was chosen after troubleshooting;
- a Zundamon or narration issue was solved by text, API, or audio-processing
  changes.

Do not record routine steps that did not involve a problem, tradeoff, or
reusable decision.

## File Location

Write to the project root `DECISIONS.md` for the repository or workspace being
processed.

- If `DECISIONS.md` exists, append a new entry.
- If it does not exist, create it.
- Do not put this operational decision log inside the skill package unless the
  task is specifically maintaining this skill repository itself.

## Entry Shape

Use concise entries with this structure:

```markdown
## YYYY-MM-DD - Short Decision Title

- Problem: <what failed or was confusing>
- Background: <source media, tool state, or constraints that mattered>
- Decision: <what approach was chosen>
- Resolution: <what command, setting, or workflow solved it>
- Verification: <how the result was checked>
- Reuse: <when to apply this again>
```

Keep absolute local paths out of the entry unless the user explicitly wants a
machine-local run log. Prefer file roles such as `source.mp4`,
`video_24fps_equal_speed.mp4`, or `audio_48k.wav`.

## Writing Rules

- Be factual and brief.
- Preserve the exact command or setting when it is the reusable part.
- Mark assumptions as `Assumption:` when not fully verified.
- Mark unresolved risks as `Follow-up:`.
- Avoid turning `DECISIONS.md` into a chronological transcript; store decisions
  and solved lessons only.
