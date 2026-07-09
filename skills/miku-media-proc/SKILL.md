---
name: miku-media-proc
description: Use when the user explicitly asks for miku-media-proc or needs a repository-local skill for media processing workflows, including video, audio, image, subtitle, metadata, and batch media preparation tasks.
---

# miku-media-proc

This skill helps Codex perform media processing work in this repository.

Use it when the user explicitly names `miku-media-proc`, asks to apply this
skill, or asks for media processing workflows that belong to this repository.

Do not use it for unrelated general coding work, generic writing, or ordinary
file inspection unless media processing is the main task.

## Environment Assumptions

Assume the working environment has these tools installed and available:

- `ffmpeg` and related FFmpeg command-line tools.
- Node.js.
- Python.
- DaVinci Resolve Studio.
- DaVinci Resolve scripting/API access, usable from automation code.

Do not spend effort installing these tools unless the user explicitly asks for
setup or troubleshooting.

## Operational Defaults

- Use `ffprobe` as the default metadata-inspection command for local video and
  audio sources before making project, timeline, frame-rate, or export
  decisions.
- Preserve source quality during working steps. Prefer stream copy, remuxing,
  original media, or lossless/high-quality intermediates over repeated lossy
  re-encoding. Use WAV/PCM for audio intermediates when reliability matters and
  the file size is acceptable.
- When timeline creation, editing, or final export is needed and no other editor
  is specified, prefer DaVinci Resolve Studio.
- For Resolve API automation, assume the user starts Resolve Studio manually,
  then connect through the API. Use Python as the default direct Resolve API
  scripting language.
- For new Resolve projects, inspect source media first, set timeline resolution
  and `timelineFrameRate` before timeline creation, then import, assemble, and
  verify placement. Decide horizontal vs vertical delivery before project or
  timeline creation. If `timelinePlaybackFrameRate` remains `24`, report it
  instead of repeatedly forcing the setting.
- For Resolve timeline assembly, consider preparing separate video-only and
  audio-only assets when source streams are unstable, audio needs WAV
  normalization, narration is added separately, or troubleshooting benefits
  from independent tracks.
- When creating task-specific automation scripts, record the script path,
  purpose, executed actions, and generated or modified Resolve project/timeline
  names in a task memo under repository-root `temp/`.
- For 60fps-family game or motion-dense footage, preserve the source fps family
  for output unless there is a concrete reason to convert.
- For local Zundamon narration, use the user-started VOICEVOX Engine HTTP API
  at `http://127.0.0.1:50021`; if the user writes `VOICEBOX`, treat it as the
  same local VOICEVOX Engine workflow unless they clarify otherwise. Verify
  `/version` and `/speakers` before synthesis. Use Zundamon normal voice as the
  default narration voice unless the user requests a different speaker or style.
  Use the local HTTP API when possible, and use Python for API-driven narration
  scripts by default.
- For generated audio or video playback checks, prefer VLC media player and use
  browser playback only as a supplemental check.
- For YouTube publishing, prepare and verify the upload candidate file locally,
  but recommend that the user uploads it manually in YouTube Studio. Automated
  YouTube upload is not recommended for this skill at this time.

## Core Workflow

1. Clarify the media task: input files, desired output format, quality target,
   constraints, and whether destructive changes are allowed.
2. Ask what kind of video or media the sources represent when it affects the
   workflow, such as game footage, screen recording, live action, commentary,
   tutorial, or social-media content.
3. Inspect source media metadata before changing files when local media files
   are available. Use FFmpeg-family tools for media inspection, and use
   `ffprobe` as the default metadata-inspection tool.
4. When timeline creation, editing, or delivery export is needed and the user
   has not specified another editor, guide the workflow toward DaVinci Resolve
   Studio first.
5. After source files are available and media information has been inspected,
   prompt the user to proceed to assembly editing in DaVinci Resolve Studio
   when a timeline deliverable is intended.
6. For final video deliverables, confirm whether the program assembly should
   include a title card, end card, or other front/back matter.
7. Prefer reproducible commands and scripts over one-off manual steps.
8. Write generated files under a task-specific output directory unless the user
   asks for an exact destination.
9. Verify output existence, duration or dimensions where relevant, and basic
   metadata before reporting completion.
10. When a media-processing problem is diagnosed and solved while using this
   skill, record the problem, background, decision, and resolution in the
   project root `DECISIONS.md`; see
   [references/integration/decisions-log.md](references/integration/decisions-log.md).

## References

- [index.json](index.json): generated inventory for this skill package.
- [references/integration/workflow.md](references/integration/workflow.md): general media-processing workflow, task routing, tool defaults, and work memo rules.
- [references/integration/decisions-log.md](references/integration/decisions-log.md): rules for recording solved media-processing decisions in project `DECISIONS.md`.
- [references/integration/frame-rate-policy.md](references/integration/frame-rate-policy.md): source frame-rate preservation and conversion policy.
- [references/davinci/resolve-24fps-recovery-case.md](references/davinci/resolve-24fps-recovery-case.md): DaVinci Resolve 24fps failure-and-recovery case for unstable smartphone or game-recorded media.
- [references/youtube/youtube-upload-fps.md](references/youtube/youtube-upload-fps.md): frame-rate guidance for YouTube upload targets.
- [references/youtube/youtube-format-duration-guidance.md](references/youtube/youtube-format-duration-guidance.md): YouTube normal video vs Shorts-style format, duration, and viewing-target guidance checked on 2026-07-09.
- [references/youtube/youtube-resolve-delivery.md](references/youtube/youtube-resolve-delivery.md): DaVinci Resolve delivery workflow for YouTube upload files.
- [references/davinci/tools-davinci-resolve.md](references/davinci/tools-davinci-resolve.md): DaVinci Resolve Studio, Python API automation, project creation, timeline fps, and assembly notes.
- [references/ffmpeg/tools-ffmpeg.md](references/ffmpeg/tools-ffmpeg.md): FFmpeg-family command reference for `ffprobe`, `ffmpeg`, and related media-preparation commands.
- [references/zundamon/tools-zundamon.md](references/zundamon/tools-zundamon.md): Zundamon narration text preparation and VOICEVOX Engine HTTP API notes.
- [references/troubleshooting/quality-check.md](references/troubleshooting/quality-check.md): verification checklist for generated media, including VLC playback checks.
- [references/VERSION.md](references/VERSION.md): source of truth for this skill version.

## Templates

- [templates/response-template.md](templates/response-template.md): concise completion report structure.
