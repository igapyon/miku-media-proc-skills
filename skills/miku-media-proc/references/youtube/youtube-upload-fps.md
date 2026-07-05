# YouTube Upload FPS Guidance

Use this reference when choosing a frame rate for YouTube delivery, especially
after creating Resolve-stable intermediate assets at a different frame rate.

## Source Rule

YouTube's official upload guidance recommends encoding and uploading content in
the same frame rate it was recorded. Common accepted frame rates include `24`,
`25`, `30`, `48`, `50`, and `60` fps.

Treat this as the default delivery rule:

- If the original content is 60fps game or smartphone footage, prefer a 60fps
  final upload when the editing/export path can produce it reliably.
- If the original content is 24fps or a film-like edit, 24fps is a normal
  delivery choice.
- If Resolve is only stable with 24fps intermediate assets, do not assume that
  24fps is also the ideal YouTube final target; decide based on content and
  export reliability.

## Practical Distinction

Separate these two decisions:

- Editing stability: choose assets that Resolve can play and render reliably.
- Delivery quality: choose the final frame rate that best preserves the source
  motion and viewer experience.

For unstable Resolve workflows, a 24fps equal-speed video plus 48kHz WAV may be
a good intermediate strategy. For final YouTube upload, 60fps is usually better
for rhythm games, action games, UI-heavy screen recordings, and other motion-
dense footage when the source was 60fps.

24fps can still be acceptable for YouTube when:

- the content is explanatory, editorial, music-video-like, or film-like;
- smoother motion is less important than edit stability;
- Resolve or the available export path cannot reliably produce a good 60fps
  output;
- the user explicitly prefers the 24fps look.

## Recommended Decision Flow

1. Inspect the source frame rate with FFmpeg.
2. If the source is 60fps and motion smoothness matters, target 60fps for the
   final YouTube upload if practical.
3. If Resolve is unstable at 60fps, use the 24fps stable workflow for editing
   or cut planning, but keep the possibility of a 60fps final render separate.
4. If only a 24fps final can be produced reliably, upload 24fps; it is accepted
   by YouTube and can be a reasonable delivery compromise.
5. Document whether 24fps was chosen for artistic/delivery reasons or as a
   Resolve stability workaround.

## Upload Encoding Notes

For ordinary SDR uploads, prefer:

- Container: MP4 with fast start.
- Video: H.264, progressive, `yuv420p`.
- Audio: AAC-LC or another YouTube-supported audio codec at `48kHz`.

When producing an intermediate Resolve asset, WAV audio is useful for editing
stability. For the final YouTube upload file, mux audio into the final video
container using a YouTube-supported delivery codec.
