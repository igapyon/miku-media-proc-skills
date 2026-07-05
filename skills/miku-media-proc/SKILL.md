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

## Core Workflow

1. Clarify the media task: input files, desired output format, quality target,
   constraints, and whether destructive changes are allowed.
2. Inspect source media metadata before changing files when local media files
   are available.
3. Prefer reproducible commands and scripts over one-off manual steps.
4. Write generated files under a task-specific output directory unless the user
   asks for an exact destination.
5. Verify output existence, duration or dimensions where relevant, and basic
   metadata before reporting completion.
6. When a media-processing problem is diagnosed and solved while using this
   skill, record the problem, background, decision, and resolution in the
   project root `DECISIONS.md`; see
   [references/integration/decisions-log.md](references/integration/decisions-log.md).

## References

- [index.json](index.json): generated inventory for this skill package.
- [references/integration/workflow.md](references/integration/workflow.md): general media-processing workflow and task routing.
- [references/integration/decisions-log.md](references/integration/decisions-log.md): rules for recording solved media-processing decisions in project `DECISIONS.md`.
- [references/davinci/resolve-24fps-stable-workflow.md](references/davinci/resolve-24fps-stable-workflow.md): DaVinci Resolve 24fps playback-stability workflow for smartphone or game-recorded media.
- [references/youtube/youtube-upload-fps.md](references/youtube/youtube-upload-fps.md): frame-rate guidance for YouTube upload targets.
- [references/youtube/youtube-resolve-delivery.md](references/youtube/youtube-resolve-delivery.md): DaVinci Resolve delivery workflow for YouTube upload files.
- [references/davinci/tools-davinci-resolve.md](references/davinci/tools-davinci-resolve.md): DaVinci Resolve Studio and API notes.
- [references/ffmpeg/tools-ffmpeg.md](references/ffmpeg/tools-ffmpeg.md): FFmpeg command patterns and media-preparation notes.
- [references/zundamon/tools-zundamon.md](references/zundamon/tools-zundamon.md): Zundamon narration text-preparation notes.
- [references/troubleshooting/quality-check.md](references/troubleshooting/quality-check.md): verification checklist for generated media.
- [references/VERSION.md](references/VERSION.md): source of truth for this skill version.

## Templates

- [templates/response-template.md](templates/response-template.md): concise completion report structure.
