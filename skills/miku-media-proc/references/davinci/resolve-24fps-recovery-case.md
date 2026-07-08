# DaVinci Resolve 24fps Recovery Case

Use this reference as a failure-and-recovery case record, not as the default
DaVinci Resolve workflow.

This document records a case where 60fps-family smartphone or game-recorded
media was difficult to handle directly in DaVinci Resolve, then the work was
recovered by creating 24fps equal-speed video and separate WAV audio assets.
Prefer the general frame-rate policy first: inspect the source, preserve the
source fps when practical, and only use this 24fps recovery pattern when Resolve
stability or API limitations make the source-fps workflow impractical.

## Observed Environment

- DaVinci Resolve Studio 21.0.2 can be controlled through the scripting API.
- On Windows, Python API access may require Resolve scripting environment
  variables and Resolve preferences.
- Resolve may report a 60fps timeline while keeping playback frame rate fixed
  at `24`; API attempts to set playback frame rate to `60` can return `False`.

## Windows API Setup

Resolve preference:

- `Preferences > System > General > External scripting using`: set to `Local`.

PowerShell environment example:

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

## Failure Pattern

60fps smartphone or game recordings can be fragile in Resolve when the playback
frame rate is effectively fixed at 24fps.

Common source characteristics:

- H.265 video.
- Vertical-video origin with rotation metadata.
- Display dimensions such as `1334x750`, backed by stored dimensions such as
  `750x1334` plus `rotation=90`.
- Real frame rate around `59.33fps` while the container reports `60fps`.
- AAC audio at `44.1kHz`, stereo.

Symptoms:

- Video appears to play slowly.
- Audio stutters or breaks up.
- Extracting audio to WAV may not solve the root cause when the playback frame
  rate mismatch remains.

Check Resolve API values:

```text
timelineFrameRate = 60.0
timelinePlaybackFrameRate = 24
```

If setting playback frame rate to 60 through the API is rejected, this case
adapted the media to the stable playback setting instead of repeatedly trying
to force the timeline.

## Recovery Asset Strategy

Create separate video and audio assets:

- Video: 24fps, H.264, `yuv420p`, equal-speed duration.
- Audio: WAV, PCM signed 16-bit, `48kHz`, stereo.

Prefer splitting the content into a video-only MP4 and an audio-only WAV before
importing it into Resolve. This keeps troubleshooting cleaner because video
frame-rate/container issues and audio sample-rate/codec issues can be isolated
independently. It also makes it easier to replace only the unstable side without
rebuilding the other asset.

Important: FFmpeg `fps=24` drops or duplicates frames to produce 24fps output;
it does not change duration by itself. This is a recovery compromise for this
class of Resolve problem, not a general recommendation to convert 60fps-family
sources to 24fps.

Example recovery command:

```powershell
ffmpeg -y -hide_banner `
  -i "source.mp4" `
  -vf "fps=24,scale=1334:750:flags=lanczos,format=yuv420p" `
  -an -c:v libx264 -preset veryfast -crf 18 -movflags +faststart `
  "video_24fps_equal_speed.mp4"

ffmpeg -y -hide_banner `
  -i "source.mp4" `
  -vn -ac 2 -ar 48000 -c:a pcm_s16le `
  "audio_48k.wav"
```

When an intermediate edit file already exists, create the WAV from the same
source used for the final video whenever possible so durations match closely.

## Resolve Timeline Placement

Resolve timelines may visually start at `01:00:00:00` instead of frame zero.
At 24fps:

```text
01:00:00:00 = 86400 frames
```

Do not blindly insert clips at `recordFrame: 0`; they may be placed before the
visible timeline start.

Prefer:

```python
record = timeline.GetStartFrame()
```

Or, when aligning to an existing timeline item:

```python
start = video_item_on_timeline.GetStart()
```

## Cut Strategy

Resolve API support for direct blade-and-delete style editing is limited.
Prefer rebuilding a new timeline instead of destructively editing the original.

For a head cut:

- Compute the cut offset in frames from the visible timeline start.
- Insert the source starting at `startFrame: <cut_offset>`.

For a tail cut after a head cut:

- Compute the desired visible duration in frames.
- Use `source_start = <head_cut_offset>`.
- Use `endFrame = source_start + cut_duration`.
- Insert with `startFrame: source_start` and `endFrame: endFrame`.

Example values from the recovered 24fps workflow:

```text
visible timeline start = 01:00:00:00
head cut at            = 01:04:30:19
head cut offset        = 6499 frames
tail cut at            = 01:02:27:14 after the head cut
cut duration           = 3542 frames
source start           = 6499
new end                = 10041
```

## Recovery Procedure

1. Inspect source metadata with FFmpeg before importing.
2. If Resolve playback is fixed at 24fps or unstable with 60fps media, create a
   24fps equal-speed video asset as a recovery workaround.
3. Extract audio as `WAV / PCM / 48kHz / stereo`.
4. Import the video-only MP4 and audio-only WAV separately so video and audio
   problems can be isolated.
5. Create a 24fps Resolve project or timeline.
6. Place video on V1 and WAV audio on A1 at `timeline.GetStartFrame()`.
7. For cuts, create new timelines from source frame ranges instead of modifying
   the original timeline in place.
8. Verify video and audio duration, placement frame, and visible timeline start.

## Do Not Generalize This Case

- Do not choose 24fps merely because this recovery worked once.
- Do not use this as the default for game footage or motion-dense screen
  recordings.
- Do not treat the intermediate recovery fps as the final delivery fps.
- If a 60fps-family workflow is stable, prefer that for 60fps-family sources.
