# FFmpeg Family Command Reference

Use this independent reference for FFmpeg-family command-line work: media
inspection, conversion, extraction, stream mapping, playback checks, and asset
preparation.

## Command Roles

- `ffprobe`: default command for structured media metadata inspection. Use this
  before project, timeline, frame-rate, or export decisions.
- `ffmpeg`: default command for conversion, extraction, remuxing, stream copy,
  audio rendering, thumbnail generation, and delivery-file preparation.
- `ffplay`: optional quick playback/debug tool when available. Prefer VLC for
  final human playback checks, especially when audio behavior matters.

Use FFmpeg-family commands before opening or automating DaVinci Resolve when
the task needs source fps, resolution, stream, codec, duration, rotation, or
audio-layout facts.

## Inspection First

Before converting, inspect:

- container format;
- video codec, dimensions, rotation metadata, frame rate, and duration;
- audio codec, sample rate, channels, and duration;
- whether the source has variable or unusual frame rate behavior.

General stream inspection command:

```bash
ffprobe -v error \
  -show_format \
  -show_streams \
  -of json \
  input.mp4
```

Frame-rate inspection command:

```bash
ffprobe -v error \
  -select_streams v:0 \
  -show_entries stream=r_frame_rate,avg_frame_rate,time_base,duration,nb_frames \
  -of default=noprint_wrappers=1 \
  input.mp4
```

Use the result to preserve the source frame rate by default. For 60fps-family
inputs, distinguish true `60` from `60000/1001`; do not round or convert unless
the workflow has a concrete reason.

Audio inspection command:

```bash
ffprobe -v error \
  -select_streams a \
  -show_entries stream=index,codec_name,sample_rate,channels,channel_layout,duration \
  -of default=noprint_wrappers=1 \
  input.mp4
```

## Conversion Discipline

- Keep source files unchanged unless the user explicitly asks to overwrite
  them.
- Prefer stream copy for remuxing or metadata/container-only work when no
  re-encode is needed.
- Avoid lossy recompression when inspection or stream copy can satisfy the
  request.
- Avoid repeated lossy generations. If a lossy encode is needed, do it as late
  as practical and keep the original or a high-quality intermediate available.
- For audio work, use WAV/PCM intermediates when reliability, editor
  compatibility, sync checks, or additional processing matters. WAV is larger
  than compressed audio, but it avoids generation loss and is predictable in
  editing tools.
- For video intermediates that must be re-encoded, choose a deliberate
  high-quality mezzanine or visually lossless setting appropriate to the tool
  chain instead of casually reusing a low-bitrate delivery encode.
- Record the exact command used in completion reports when it matters for
  reproducibility.

Common remux pattern:

```bash
ffmpeg -y -hide_banner \
  -i input.mov \
  -map 0 \
  -c copy \
  output.mp4
```

Audio extraction to WAV/PCM:

```bash
ffmpeg -y -hide_banner \
  -i input.mp4 \
  -vn -ac 2 -ar 48000 -c:a pcm_s16le \
  audio_48k.wav
```

This produces a large but predictable audio intermediate that avoids additional
lossy generation.

Stream-copy video-only extraction when the source video stream is already
suitable:

```bash
ffmpeg -y -hide_banner \
  -i input.mp4 \
  -an -c:v copy \
  video_only.mp4
```

## Timeline Assembly Preparation Pattern

For Resolve timeline assembly, it can be useful to split input into separate
assets before import:

- video-only MP4;
- audio-only WAV.

This is useful when the source container is unstable, audio needs conversion to
WAV, narration or replacement audio is produced separately, or troubleshooting
benefits from independent video/audio placement. Video frame-rate/container
problems and audio sample-rate/codec problems can be tested independently, and
only the unstable side needs to be regenerated.

Common video-only extraction/transcode pattern:

```bash
ffmpeg -y -hide_banner \
  -i input.mp4 \
  -an -c:v copy \
  video_only.mp4
```

When stream copy is not suitable, re-encode deliberately and record why.

24fps equal-speed video from the Resolve recovery case:

```powershell
ffmpeg -y -hide_banner `
  -i "source.mp4" `
  -vf "fps=24,scale=1334:750:flags=lanczos,format=yuv420p" `
  -an -c:v libx264 -preset veryfast -crf 18 -movflags +faststart `
  "video_24fps_equal_speed.mp4"
```

48kHz stereo WAV:

```powershell
ffmpeg -y -hide_banner `
  -i "source.mp4" `
  -vn -ac 2 -ar 48000 -c:a pcm_s16le `
  "audio_48k.wav"
```

Important: `fps=24` changes frame cadence by dropping or duplicating frames; it
does not change duration by itself.

## Delivery Notes

- Use MP4 with fast start for common upload delivery.
- Use H.264 and `yuv420p` for broadly compatible SDR video.
- Use WAV for editing intermediates when useful, but mux final delivery audio
  into the upload file with a delivery codec appropriate for the target.
- Do not leave final viewer-facing delivery split into separate video and audio
  files unless the user explicitly requests a separate-deliverable workflow.

## Related References

- [../davinci/resolve-24fps-recovery-case.md](../davinci/resolve-24fps-recovery-case.md): FFmpeg
  commands from a Resolve 24fps recovery case.
- [../integration/frame-rate-policy.md](../integration/frame-rate-policy.md): general source-fps
  preservation and conversion policy.
- [../youtube/youtube-upload-fps.md](../youtube/youtube-upload-fps.md): final upload frame-rate
  decision rules.
