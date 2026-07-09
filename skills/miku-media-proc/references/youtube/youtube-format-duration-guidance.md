# YouTube Format and Duration Guidance

This reference records YouTube format and duration guidance checked on
2026-07-09. Treat platform limits here as planning defaults. Re-check official
YouTube Help before finalizing a new publishing workflow or upload candidate,
because platform behavior changes over time.

## Sources Checked

- YouTube Help: Get started creating YouTube Shorts
  - https://support.google.com/youtube/answer/10059070
- YouTube Help: YouTube recommended upload encoding settings
  - https://support.google.com/youtube/answer/1722171
- YouTube Help: Upload videos longer than 15 minutes
  - https://support.google.com/youtube/answer/71673
- YouTube Help: Video resolution & aspect ratios
  - https://support.google.com/youtube/answer/6375112

## YouTube Format Families

Treat YouTube deliverables as at least two practical families:

- Long-form or normal YouTube video: commonly horizontal `16:9`, such as
  `1920x1080`, `2560x1440`, or `3840x2160`. This is suitable for performances,
  screen walkthroughs, tutorials, commentary, reviews, and other content where
  the viewer is expected to stay longer.
- Shorts-style short vertical video: commonly vertical `9:16`, such as
  `1080x1920`. This is suitable for quick highlights, hooks, before/after clips,
  short explanations, and smartphone-first viewing.

YouTube can adapt its player to non-`16:9` videos, including vertical and square
videos. Do not bake black bars or padding into the video unless the user
explicitly wants that look; padding can prevent YouTube from adapting the player
well to the viewer's device.

## YouTube Platform Limits Checked On 2026-07-09

YouTube Shorts:

- YouTube Help describes Shorts creation tools as supporting short-form videos
  up to `3 minutes`.
- YouTube Help states that Short videos can be uploaded with a maximum
  resolution of `1080p`.
- The older `60 seconds` mental model is stale as a platform limit, but it
  remains useful as a practical target range.

Normal YouTube uploads:

- By default, YouTube accounts can upload videos up to `15 minutes`.
- Verified accounts can upload videos longer than `15 minutes`.
- YouTube Help states the maximum upload size as `256 GB` or `12 hours`,
  whichever is less.

Before delivery, re-check the current official YouTube Help pages when the
answer depends on eligibility, maximum duration, maximum file size, Shorts
classification, or upload resolution. These limits should not be treated as
permanent constants.

Encoding recommendations:

- Container: MP4 with the moov atom at the front of the file, often called Fast
  Start.
- Audio: AAC-LC, Opus, or another YouTube-supported upload codec; `48kHz` is
  recommended.
- Video: H.264 is a common recommended upload codec for SDR.
- Frame rate: encode and upload in the same frame rate as the content was
  recorded when possible. Common frame rates include `24`, `25`, `30`, `48`,
  `50`, and `60` fps.
- For `16:9`, common resolutions include `1920x1080`, `1280x720`,
  `2560x1440`, and `3840x2160`.

## Practical Viewing Constraints

Platform limits are not the same as good viewing targets.

For Shorts-style vertical videos:

- `15-30 seconds`: strong target for a single idea, highlight, hook, or punchy
  demonstration.
- `30-60 seconds`: practical default for short explanations, quick edits, and
  small narratives.
- `60-180 seconds`: possible under the checked YouTube Shorts limit, but use it
  only when the content has enough structure to keep attention.

For normal horizontal videos:

- Favor clarity over arbitrary shortness.
- Long-form video is better when the viewer needs context, continuity,
  readable UI detail, musical performance length, or step-by-step explanation.
- A horizontal video can still be short; format and duration should follow the
  viewing situation, not only the platform category.

## Production Decision Rules

Before editing or exporting, decide:

1. Target family: normal YouTube video, Shorts-style vertical, or both.
2. Target aspect ratio and resolution.
3. Target duration range.
4. Whether the source should be reframed, cropped, letterboxed, or edited into a
   separate vertical composition.
5. Whether the source frame rate should be preserved.
6. Whether the output is an upload candidate or only an editing intermediate.

Default choices:

- Use `16:9` horizontal for ordinary YouTube upload unless the user asks for
  Shorts-style output or the content is clearly smartphone-first.
- Use `9:16` vertical for Shorts-style output.
- Prefer `1080x1920` for Shorts-style vertical unless the user has a different
  target.
- Prefer preserving source fps, especially for game footage, screen recordings,
  rhythm/action content, or motion-dense clips.
- Avoid forcing a long horizontal video into Shorts without making an explicit
  crop/reframe decision. Important UI, subtitles, hands, instruments, and faces
  may be lost.

## Agent Workflow Notes

When the user says "YouTube":

- Ask whether they mean normal horizontal YouTube, Shorts-style vertical, or
  both if the target is ambiguous.
- If the user says "Shorts", "short", "vertical", "reel", or "SNS", discuss
  vertical framing and short duration targets before export.
- If making both long-form and Shorts-style versions, treat them as separate
  deliverables with separate timelines or render presets.
- Verify final duration, resolution, aspect ratio, frame rate, audio presence,
  and whether the output has the intended title card or end card.
- Prepare the local upload candidate, then recommend manual upload in YouTube
  Studio. Do not automate YouTube upload by default.

Do not treat `30 seconds`, `60 seconds`, or `3 minutes` as universal goals.
They are decision anchors. The actual target should match the content and the
user's publishing intent.
