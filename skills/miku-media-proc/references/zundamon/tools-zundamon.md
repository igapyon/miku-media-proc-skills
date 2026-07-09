# Zundamon Narration Tool Notes

Use this reference when preparing text for Zundamon-style narration or similar
text-to-speech workflows.

## Local API Assumption

For local narration, default to Zundamon normal voice through the user-started
local VOICEBOX/VOICEVOX Engine HTTP API. Assume the Windows application or
engine is started by the user and exposes:

```text
http://127.0.0.1:50021
```

Do not assume an MCP server is available. If MCP integration is needed later,
wrap the local HTTP API instead of inventing a separate speech workflow.

Use the local HTTP API when possible. Use Python as the default language for
API-driven narration scripts because it is straightforward for HTTP requests,
file writing, WAV handling, and integration with Resolve-oriented Python
automation. Use shell `curl` only for short checks or handoff examples unless a
Python script would be unnecessary.

If the user writes `VOICEBOX`, treat it as the user-facing name for the same
local VOICEVOX Engine workflow unless they clarify that they mean a different
tool.

Useful local endpoints:

- `GET /version`: confirm that the local engine is reachable.
- `GET /speakers`: list speakers and style IDs; use this to confirm the
  Zundamon `speaker` ID for the installed version.
- `POST /audio_query?speaker=<id>`: create the synthesis query from text.
- `POST /synthesis?speaker=<id>`: synthesize WAV audio from the query JSON.
- `GET /docs`: inspect the local API documentation when the engine is running.

Default narration voice:

- Character: Zundamon.
- Style: normal.
- Common speaker ID: `3`.

Confirm the available speaker/style ID with `/speakers` for the user's
installed engine before relying on the common ID.

## Main Rule

Raw explanatory text is often not the best input for narration. Before
synthesis, have a generative AI rewrite or edit the script for read-aloud
smoothness.

The goal is not to change the meaning. The goal is to make the text easier for
the voice engine to speak naturally.

## Text Preparation

Prefer:

- shorter sentences;
- clear subject and predicate pairs;
- natural spoken Japanese;
- explicit readings for difficult names when needed;
- fewer nested clauses;
- punctuation that helps pacing;
- spelling or wording that avoids likely misreadings.

Avoid:

- long written-style sentences;
- dense technical noun chains;
- ambiguous abbreviations;
- symbols that the voice engine may read awkwardly;
- raw Markdown or code-like text unless the narration intentionally explains it.

## Workflow

1. Draft the technical or explanatory content normally.
2. Ask a generative AI to rewrite it for Zundamon narration while preserving
   meaning and terminology.
3. Review terms that must not change.
4. Confirm the local VOICEBOX/VOICEVOX Engine is reachable.
5. Confirm the intended Zundamon normal speaker/style ID via `/version` and
   `/speakers`; default to normal voice unless the user requests a different
   style.
6. Run a short synthesis sample before processing the full script.
7. If pronunciation or pacing is bad, adjust text first before changing audio
   processing.

## Minimal HTTP Flow

PowerShell example:

```powershell
curl -s -X POST `
  "http://127.0.0.1:50021/audio_query?speaker=<speaker-id>" `
  --get --data-urlencode "text=こんにちはなのだ" `
  > query.json

curl -s -H "Content-Type: application/json" `
  -X POST `
  -d @query.json `
  "http://127.0.0.1:50021/synthesis?speaker=<speaker-id>" `
  > audio.wav
```

Python-oriented API scripts should perform the same sequence:

1. `GET /version`.
2. `GET /speakers`.
3. `POST /audio_query?speaker=<id>`.
4. `POST /synthesis?speaker=<id>`.
5. Write the returned WAV bytes to a task output path.

The generated WAV may use a sample rate that is not the final video target.
For Resolve or YouTube workflows, convert or render narration audio to the
project target, commonly `48kHz`, before final delivery.

## Completion Check

Confirm:

- the script is easy to read aloud;
- important terms remain intact;
- difficult readings are clarified;
- the chosen speaker/style ID is recorded;
- a short WAV sample was generated successfully;
- no unintended meaning change was introduced.
