# DaVinci Resolve 24fps Stable Workflow

Use this reference when DaVinci Resolve Studio playback is unstable with
smartphone or game-recorded media, especially when 60fps footage appears slow,
audio stutters, or a WAV extraction alone does not fix playback.

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

## Problem Pattern

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

If setting playback frame rate to 60 through the API is rejected, adapt the
media to the stable playback setting instead of repeatedly trying to force the
timeline.

## Stable Asset Strategy

Create separate video and audio assets:

- Video: 24fps, H.264, `yuv420p`, equal-speed duration.
- Audio: WAV, PCM signed 16-bit, `48kHz`, stereo.

Prefer splitting the content into a video-only MP4 and an audio-only WAV before
importing it into Resolve. This keeps troubleshooting cleaner because video
frame-rate/container issues and audio sample-rate/codec issues can be isolated
independently. It also makes it easier to replace only the unstable side without
rebuilding the other asset.

Important: FFmpeg `fps=24` drops or duplicates frames to produce 24fps output;
it does not change duration by itself. Use it when the desired result is an
equal-speed 24fps video for a 24fps Resolve workflow.

Example for a Resolve-24fps-stable source:

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

Example values from a 24fps workflow:

```text
visible timeline start = 01:00:00:00
head cut at            = 01:04:30:19
head cut offset        = 6499 frames
tail cut at            = 01:02:27:14 after the head cut
cut duration           = 3542 frames
source start           = 6499
new end                = 10041
```

## Recommended Procedure

1. Inspect source metadata with FFmpeg before importing.
2. If Resolve playback is fixed at 24fps or unstable with 60fps media, create a
   24fps equal-speed video asset.
3. Extract audio as `WAV / PCM / 48kHz / stereo`.
4. Import the video-only MP4 and audio-only WAV separately so video and audio
   problems can be isolated.
5. Create a 24fps Resolve project or timeline.
6. Place video on V1 and WAV audio on A1 at `timeline.GetStartFrame()`.
7. For cuts, create new timelines from source frame ranges instead of modifying
   the original timeline in place.
8. Verify video and audio duration, placement frame, and visible timeline start.
