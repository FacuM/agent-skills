---
description: Extract and explain steps from a YouTube video
argument-hint: [youtube-url] [analysis-focus]
---

Use the **youtube-step-extractor** skill to analyze the video at `$1`.

Analysis focus and other invocation context: `$ARGUMENTS`

1. If no valid YouTube URL is available in the arguments or conversation, request one before continuing.
2. Read repository instructions and confirm the required download and frame-extraction dependencies.
3. Use the scripts bundled with the installed skill, resolving their paths relative to the skill directory.
4. Choose a frame rate appropriate for the video's length and the requested level of detail.
5. Inspect representative frames and use subtitles or a transcript when they materially improve accuracy.
6. Return a numbered, chronological workflow with timestamps or frame references when available.
7. Clean up temporary media unless the user asks to keep it.

Do not infer invisible actions from sparse frames; mark uncertainty explicitly.
