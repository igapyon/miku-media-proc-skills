# Frame Rate Policy

Use this reference when deciding whether to keep, convert, or normalize video
frame rate during media-processing work.

## Core Principle

Respect the source frame rate by default.

Before choosing any processing or delivery frame rate, inspect the input with
FFmpeg or `ffprobe`. Do not infer frame rate from a file name, capture device,
or editing timeline alone.

Useful inspection command:

```bash
ffprobe -v error \
  -select_streams v:0 \
  -show_entries stream=r_frame_rate,avg_frame_rate,time_base,duration,nb_frames \
  -of default=noprint_wrappers=1 \
  input.mp4
```

Check both `r_frame_rate` and `avg_frame_rate`, and watch for variable or
unusual frame-rate behavior.

## 60fps and 59.94fps Sources

When the input is `60` fps or near 60 fps, preserve the 60fps family through
the workflow unless there is a clear reason to do otherwise.

- If the source is true `60` fps, prefer `60` fps for intermediate and final
  video.
- If the source is `60000/1001` fps, prefer `60000/1001` rather than rounding
  to `60`.
- If the measured average is near 60 but inconsistent, treat it as a source
  property to investigate before normalizing.

Do not casually mix `60` and `60000/1001`. The difference is small, but it can
matter for long durations, audio sync, subtitles, frame-accurate cuts, and
multi-source edits.

## Games and Screen Recordings

For game footage, rhythm games, action games, UI-heavy recordings, and other
motion-dense screen captures, choose a reasonably high frame rate when the
source supports it.

Default expectations:

- 60fps-family source: keep the 60fps family.
- 120fps or higher source: preserve it for archive, analysis, slow motion, or
  high-fidelity workflows; consider 60fps-family delivery for common upload
  targets when size, compatibility, or platform limits matter.
- 30fps source: keep 30fps unless there is a specific reason to convert.

Do not up-convert low-fps game footage to a higher fps as if new motion detail
were created. Only do so for a specific delivery requirement or an explicit
motion-interpolation workflow.

## When Conversion Is Acceptable

Convert frame rate only for a concrete reason, such as:

- editing software stability;
- platform or device compatibility;
- file size or bitrate constraints;
- artistic look;
- slow-motion or speed-change work;
- making a variable-frame-rate source easier to process as constant frame rate.

Document whether a conversion is a delivery decision, an editing workaround, or
an artistic choice.

## Intermediate vs Final Frame Rate

Keep the intermediate workflow aligned with the source frame rate when possible.
If an editing tool requires a different intermediate frame rate for stability,
treat that as a workaround, not automatically as the final delivery target.

For example, a 24fps Resolve recovery intermediate can be useful after a failed
source-fps workflow, but a 60fps-family game source may still deserve a
60fps-family final output when the render path can produce it reliably.
