# FFmpeg Tool Notes

Use this reference for FFmpeg inspection, conversion, and asset preparation.

## Inspection First

Before converting, inspect:

- container format;
- video codec, dimensions, rotation metadata, frame rate, and duration;
- audio codec, sample rate, channels, and duration;
- whether the source has variable or unusual frame rate behavior.

## Resolve Preparation Pattern

For Resolve troubleshooting, prefer splitting input into separate assets before
import:

- video-only MP4;
- audio-only WAV.

This makes failures easier to isolate. Video frame-rate/container problems and
audio sample-rate/codec problems can be tested independently, and only the
unstable side needs to be regenerated.

24fps equal-speed video for a Resolve workflow:

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

- [../davinci/resolve-24fps-stable-workflow.md](../davinci/resolve-24fps-stable-workflow.md): FFmpeg
  commands used for Resolve-stable assets.
- [../youtube/youtube-upload-fps.md](../youtube/youtube-upload-fps.md): final upload frame-rate
  decision rules.
