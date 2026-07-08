# DaVinci Resolve Tool Notes

Use this reference for DaVinci Resolve Studio and Resolve API work.

## Product Requirement

Treat DaVinci Resolve Studio as the expected edition for API-driven workflows.
In observed work, the paid Studio edition was required before Resolve API access
was usable. If API connection fails, verify the edition and scripting settings
before debugging media processing logic.

## API Setup Notes

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

- Inspect both timeline frame rate and playback frame rate.
- If `timelinePlaybackFrameRate` remains `24` and API attempts to change it
  return `False`, adapt the media or workflow instead of repeatedly forcing the
  setting.
- Use `timeline.GetStartFrame()` for visible timeline placement. Do not assume
  `recordFrame: 0` is visible; Resolve timelines may start at `01:00:00:00`.
- For API-based cuts, prefer rebuilding a new timeline from source frame ranges
  over destructive edits to the original timeline.

## Related References

- [resolve-24fps-recovery-case.md](resolve-24fps-recovery-case.md): failure-and-recovery
  case where 24fps assets were used after Resolve playback was unstable with
  60fps-family sources.
