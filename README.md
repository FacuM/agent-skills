# Agent Skills Repository

A collection of small, task-focused skills you can drop into your workflow. Each skill lives in its own folder with a `SKILL.md` and supporting scripts.

## Available Skills

- `php-optimization-engineer` - Automatically analyze and optimize PHP code for performance, memory, database queries, and caching.
- `youtube-step-extractor` - Download a YouTube video, extract frames, and analyze visual steps.

## Using A Skill

1. Open the skill folder (for example, `youtube-step-extractor`).
2. Follow the workflow in its `SKILL.md`.
3. Run any bundled scripts from that skill's `scripts/` directory.

Example for the YouTube step extractor:

```bash
./youtube-step-extractor/scripts/download_and_extract.sh \
  "https://www.youtube.com/watch?v=VIDEO_ID" \
  /tmp/yt_frames \
  1
```

## Requesting A Skill In A Prompt

When asking the agent to run a skill, mention the skill name and the input it needs.

Example prompt:

```
Use the youtube-step-extractor skill to analyze the steps shown in this video:
https://www.youtube.com/watch?v=VIDEO_ID
```

## License

MIT. See [LICENSE](LICENSE).
