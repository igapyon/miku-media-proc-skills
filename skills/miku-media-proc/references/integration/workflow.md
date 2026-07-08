# miku-media-proc Workflow

## Environment

Assume `ffmpeg`, related FFmpeg command-line tools, Node.js, Python, DaVinci
Resolve Studio, and DaVinci Resolve scripting/API access are already installed
and usable.

Use FFmpeg for direct media inspection, conversion, extraction, stream mapping,
container changes, thumbnails, and batch processing. Use DaVinci Resolve Studio
via API when the task involves Resolve projects, timelines, bins, render jobs,
color-managed workflows, or operations that should happen inside Resolve.
Use Node.js for automation scripts, batch orchestration, JSON processing, and
Resolve scripting glue when it fits the existing project style.
Use Python for DaVinci Resolve scripting/API access when Resolve's Python
module is the most direct integration path.

When deciding whether to keep, normalize, or convert video frame rate, read
[frame-rate-policy.md](frame-rate-policy.md). Respect the source frame rate by
default, verify it with FFmpeg or `ffprobe`, and preserve the 60fps family for
game or motion-dense footage unless there is a concrete reason to convert.

When DaVinci Resolve playback is unstable with smartphone or game-recorded
media, especially when 60fps sources play slowly or audio stutters, read
[../davinci/resolve-24fps-recovery-case.md](../davinci/resolve-24fps-recovery-case.md) as a
failure-and-recovery case record before choosing a recovery strategy.

When preparing a final upload for YouTube, read
[../youtube/youtube-upload-fps.md](../youtube/youtube-upload-fps.md) before choosing whether to keep
the recorded frame rate, export at 60fps, or accept a 24fps delivery.
When DaVinci Resolve is used to create the YouTube upload file, also read
[../youtube/youtube-resolve-delivery.md](../youtube/youtube-resolve-delivery.md).

When a task centers on a specific tool, read the matching tool note:

- DaVinci Resolve Studio or Resolve API: [../davinci/tools-davinci-resolve.md](../davinci/tools-davinci-resolve.md)
- FFmpeg conversion or inspection: [../ffmpeg/tools-ffmpeg.md](../ffmpeg/tools-ffmpeg.md)
- Zundamon narration text: [../zundamon/tools-zundamon.md](../zundamon/tools-zundamon.md)

When a problem is diagnosed and solved in a reusable way, record it in the
project root `DECISIONS.md` using [decisions-log.md](decisions-log.md).

## Task Routing

- Video: inspect container, streams, duration, dimensions, frame rate, codec,
  bitrate, and audio layout before conversion.
- Program assembly: for final video deliverables, confirm whether to include a
  title card, end card, countdown, slate, or other front/back matter before
  rendering the delivery file.
- Frame rate: verify the source fps with FFmpeg or `ffprobe`; keep the source
  fps by default, and avoid mixing `60` with `60000/1001` without an explicit
  reason.
- Resolve: inspect timeline frame rate, playback frame rate, timeline start
  frame, and whether API edits should rebuild a new timeline instead of
  destructively modifying the current one.
- YouTube: distinguish Resolve recovery or editing-stability assets from final
  upload assets; do not assume the intermediate Resolve fps is the best
  delivery fps.
  Confirm the upload range before rendering: full timeline, in/out range, or a
  rebuilt timeline that already represents the upload segment.
- Zundamon: prepare narration text for speech smoothness before synthesis;
  prefer AI-assisted wording cleanup when the user's raw draft is hard to read
  aloud.
- Audio: inspect codec, sample rate, channel layout, loudness requirements, and
  target delivery format before conversion.
- Image: inspect dimensions, color mode, transparency, and target size before
  resizing or format conversion.
- Subtitles: inspect encoding, timing format, language, and target container.
- Metadata: preserve source metadata unless the user asks to strip or rewrite it.
- Batch work: run on a small sample first when the operation is risky or slow.

## Operating Rules

- Keep source files unchanged unless the user explicitly asks to overwrite them.
- Prefer deterministic output names that include the operation or target format.
- Use task-specific output directories such as `workplace/<task-name>/` when no
  destination is specified.
- Avoid lossy recompression when stream copy or metadata-only changes satisfy
  the request.
- Do not add title cards, end cards, or slates without user intent; prompt for
  them when producing a final viewer-facing video.
- Record the exact command or script used when reporting results.

## Escalation Points

Ask the user before proceeding when:

- The requested output would overwrite source media.
- The task requires large dependency installation.
- The correct quality target is ambiguous and would materially affect output.
- The source appears damaged, encrypted, incomplete, or inconsistent.
