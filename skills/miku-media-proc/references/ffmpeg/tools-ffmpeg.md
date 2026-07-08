# FFmpeg Tool Notes

Use this reference for FFmpeg inspection, conversion, and asset preparation.

## Inspection First

Before converting, inspect:

- container format;
- video codec, dimensions, rotation metadata, frame rate, and duration;
- audio codec, sample rate, channels, and duration;
- whether the source has variable or unusual frame rate behavior.

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

## Resolve Preparation Pattern

For Resolve troubleshooting, prefer splitting input into separate assets before
import:

- video-only MP4;
- audio-only WAV.

This makes failures easier to isolate. Video frame-rate/container problems and
audio sample-rate/codec problems can be tested independently, and only the
unstable side needs to be regenerated.

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

## Related References

- [../davinci/resolve-24fps-recovery-case.md](../davinci/resolve-24fps-recovery-case.md): FFmpeg
  commands from a Resolve 24fps recovery case.
- [../integration/frame-rate-policy.md](../integration/frame-rate-policy.md): general source-fps
  preservation and conversion policy.
- [../youtube/youtube-upload-fps.md](../youtube/youtube-upload-fps.md): final upload frame-rate
  decision rules.
