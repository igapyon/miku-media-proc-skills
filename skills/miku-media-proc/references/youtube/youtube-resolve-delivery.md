# YouTube Delivery From DaVinci Resolve

Use this reference when creating the final file for YouTube upload from
DaVinci Resolve.

## Decide The Upload Range

Before rendering, explicitly decide what range is the upload target.

Common choices:

- Full timeline: use when the timeline already contains exactly the final
  upload content.
- In/out range: use when only a selected segment of the timeline should be
  uploaded.
- Rebuilt delivery timeline: use when API-based trimming or source-range
  assembly created a new timeline that already represents the upload target.

Do not assume the visible timeline is the intended upload range. Ask or infer
from the user's latest instruction whether the upload target is the full
timeline or a bounded range.

For API workflows, prefer a rebuilt delivery timeline when the edit consists of
head/tail cuts. This avoids ambiguity around in/out state and keeps the upload
target inspectable as its own timeline.

## Recommended Resolve Delivery Flow

1. Confirm the timeline or range that should be uploaded.
2. Confirm final delivery fps using [youtube-upload-fps.md](youtube-upload-fps.md).
3. Confirm whether the program assembly should include a title card or end card.
4. Confirm final audio is present and aligned.
5. Render to a local output file intended for upload, not to an intermediate
   troubleshooting asset.
6. Inspect the rendered file with FFmpeg.
7. If the output is acceptable, treat that file as the YouTube upload candidate.

## Program Assembly Notes

Use production terminology consistently:

- Title card: an opening visual that names the video, piece, episode, or topic.
- End card: a closing visual used for credits, next action, related links, or a
  clean ending.
- Slate: a production/technical marker; do not include it in the public upload
  unless explicitly requested.

For viewer-facing YouTube files, ask whether a title card and end card are
needed before final rendering. Keep them out when the user wants a raw clip,
archive file, or direct gameplay capture.

## Render Target Notes

Prefer ordinary YouTube-compatible SDR output unless the user asks for a
different delivery target:

- Container: MP4.
- Video: H.264, progressive, `yuv420p`.
- Audio: AAC in the final upload file.
- Audio sample rate: `48kHz`.
- Fast-start MP4 is preferred for upload compatibility.

Use WAV for Resolve/editing intermediates when helpful, but do not leave the
YouTube upload candidate as separate MP4 and WAV files. The final upload
candidate should contain both video and audio in one file unless the user has a
specific separate-upload workflow.

## Range Discipline

When using Resolve API:

- Record the chosen delivery timeline name.
- Record whether the render target is full timeline or a specific frame range.
- Use `timeline.GetStartFrame()` to reason about visible frame positions.
- If a clip was placed from source ranges, record the source start and end
  frames used to build the delivery timeline.

When using manual Resolve operations:

- Check the Deliver page range setting before rendering.
- Confirm whether in/out marks are active.
- Clear in/out marks or switch to full timeline if the whole timeline should be
  uploaded.

## FFmpeg Verification

After rendering, inspect:

- container and codecs;
- duration;
- frame rate;
- dimensions and rotation metadata;
- audio stream presence;
- audio sample rate and channels.
- expected title card and end card presence when they were requested.

For YouTube upload candidates, also check that the rendered duration matches
the intended upload range closely enough for the task.

## Relationship To Recovery Intermediates

The Resolve 24fps recovery case may use a video-only MP4 and an audio-only WAV
for editing and troubleshooting. That is not the final YouTube upload shape.

For delivery, render or mux a single upload file after the target range is
confirmed.
