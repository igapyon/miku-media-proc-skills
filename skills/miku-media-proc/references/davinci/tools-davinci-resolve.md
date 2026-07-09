# DaVinci Resolve Tool Notes

Use this reference for DaVinci Resolve Studio and Resolve API work.

## Product Requirement

Treat DaVinci Resolve Studio as the expected edition for API-driven workflows.
In observed work, the paid Studio edition was required before Resolve API access
was usable. If API connection fails, verify the edition and scripting settings
before debugging media processing logic.

## API Setup Notes

DaVinci Resolve Studio startup is normally a human action. Ask the user to open
Resolve Studio first, then connect through the Resolve API after the
application is running. After API connection, automation may create projects,
import media, create timelines, assemble clips, configure export settings, and
run placement checks.

Use Python as the default language for direct Resolve API automation scripts.
Resolve's official scripting API is Python/Lua based, so Python should be the
normal "source of truth" for scripts that directly call Resolve. Node.js may be
used for surrounding orchestration, JSON processing, or experiments, but should
not replace the Python Resolve API script without a concrete reason.

Resolve preference:

- `Preferences > System > General > External scripting using`: set to `Local`.

Windows PowerShell environment example:

```powershell
$env:PYTHONIOENCODING='utf-8'
$env:PYTHONUTF8='1'
$env:PYTHONPATH='C:\ProgramData\Blackmagic Design\DaVinci Resolve\Support\Developer\Scripting\Modules'
$env:RESOLVE_SCRIPT_API='C:\ProgramData\Blackmagic Design\DaVinci Resolve\Support\Developer\Scripting'
$env:RESOLVE_SCRIPT_LIB='C:\Program Files\Blackmagic Design\DaVinci Resolve\fusionscript.dll'
```

Connection smoke check:

```powershell
py -3.11 -c "import DaVinciResolveScript as dvr; r=dvr.scriptapp('Resolve'); print(bool(r)); print(r.GetVersionString() if r else '')"
```

## Timeline Notes

- Before creating a new Resolve project, inspect source fps, resolution, codec,
  duration, and audio information with `ffprobe`. Use that information to
  choose the project settings and timeline frame rate before importing media
  and assembling clips.
- Before creating a new Resolve project, decide the target delivery family and
  timeline geometry: normal horizontal YouTube, Shorts-style vertical, or both.
  Resolve timeline frame rate and resolution are easier to set correctly before
  timeline creation than to repair later.
- For a new project, set `timelineFrameRate` with `SetSetting` immediately
  after project creation and before timeline creation. Then import media,
  create the timeline, assemble the clips, and verify placement.
- It is acceptable to import video and audio as separate media files for
  timeline assembly. Prefer separate video-only and audio-only assets when the
  source container is unstable, audio needs conversion to WAV, narration or
  replacement audio is produced separately, or troubleshooting requires
  independent video/audio placement.
- Inspect both timeline frame rate and playback frame rate.
- If `timelinePlaybackFrameRate` remains `24` and API attempts to change it
  return `False`, do not repeatedly force it through the API. Inspect the
  timeline settings and placement result, then clearly report the remaining
  playback-frame-rate value to the user.
- Frame-rate changes after timeline creation are constrained in Resolve. If the
  target fps is wrong, prefer creating a new project or new timeline at the
  intended fps and reassembling the material instead of trying to mutate an
  existing timeline in place.
- Use `timeline.GetStartFrame()` for visible timeline placement. Do not assume
  `recordFrame: 0` is visible; Resolve timelines may start at `01:00:00:00`.
- For API-based cuts, prefer rebuilding a new timeline from source frame ranges
  over destructive edits to the original timeline.

## New Project Assembly Flow

Use this order for new Resolve project automation:

1. Inspect all source media with `ffprobe`.
2. Ask or infer the content type only when needed for fps, subtitle, audio, or
   export policy.
3. Decide whether the target is normal horizontal video, Shorts-style vertical
   video, or both. Choose timeline resolution and fps before project/timeline
   creation.
4. Ask the user to open DaVinci Resolve Studio if it is not already running.
5. Connect to Resolve through the Python API.
6. Create the project.
7. Set timeline resolution and `timelineFrameRate` before timeline creation.
8. Import media. Use either combined media or separate video-only/audio-only
   assets depending on the source and edit plan.
9. Create the timeline.
10. Assemble video and audio clips.
11. Verify timeline placement, timeline resolution, timeline fps, playback fps,
    and saved project state.
12. Report the project name, timeline name, source media summary, and any
    unresolved setting mismatch such as `timelinePlaybackFrameRate` staying at
    `24`.

## Related References

- [resolve-24fps-recovery-case.md](resolve-24fps-recovery-case.md): failure-and-recovery
  case where 24fps assets were used after Resolve playback was unstable with
  60fps-family sources.
