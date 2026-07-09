# miku-media-proc Quality Check

Use checks that match the requested output.

## Required Checks

- Confirm the output file exists and is non-empty.
- Confirm the expected format or extension.
- Confirm duration for video and audio outputs.
- Confirm dimensions for video and image outputs.
- Confirm stream count when video, audio, or subtitle streams are expected.
- For final viewer-facing video, confirm whether expected title cards, end
  cards, slates, or other front/back matter are present or intentionally absent.

## Recommended Checks

- Compare source and output duration for transcodes.
- Check that audio was not unintentionally dropped.
- Check that transparency was preserved for image formats that support it.
- Spot-check a rendered frame, thumbnail, waveform, or short preview when visual
  or audible quality matters.
- Spot-check the first and last visible seconds of final video deliverables.
- For generated audio or video playback checks, prefer VLC media player as the
  primary local playback tool. Use browser playback only as a supplemental
  check, especially when audio output or playback behavior is uncertain.

## Completion Report

Report:

- Input path or input set.
- Output path.
- Main operation performed.
- Verification performed.
- Any caveats or skipped checks.
