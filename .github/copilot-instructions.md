# Project Guidelines

## Code Style
- Shell scripts use `bash` with `set -e` and clear usage messages; keep that pattern in [youtube-step-extractor/scripts/download_and_extract.sh](../youtube-step-extractor/scripts/download_and_extract.sh) and [youtube-step-extractor/scripts/extract_frames.sh](../youtube-step-extractor/scripts/extract_frames.sh).
- Prefer explicit dependency checks (`command -v`) and informative error output before doing work.

## Architecture
- Single-skill folder layout: each versioned skill has a `SKILL.md` and only the `scripts/` or `references/` resources required by its workflow.
- [web-performance-optimizer/SKILL.md](../web-performance-optimizer/SKILL.md) is the combined web-performance workflow; its detailed checklist, objective loop, and evidence template live under [web-performance-optimizer/references](../web-performance-optimizer/references).
- [commands](../commands) contains matching Claude Code slash commands. Keep command documentation in `README.txt`, not Markdown, so it is not discovered as another slash command when copied.
- The YouTube step flow is: download with `yt-dlp` -> extract frames with `ffmpeg` -> analyze frames; scripts orchestrate this in [youtube-step-extractor/scripts/download_and_extract.sh](../youtube-step-extractor/scripts/download_and_extract.sh).

## Build and Test
- No automated tests present.
- Validate edited skills with a skill frontmatter validator when available, then verify command-to-skill name parity and run `git diff --check`.
- Manual run (Linux/macOS):
  - `./youtube-step-extractor/scripts/download_and_extract.sh "YOUTUBE_URL" /tmp/yt_frames 1`
  - `./youtube-step-extractor/scripts/extract_frames.sh /tmp/yt_video.mp4 /tmp/yt_frames 1`

## Project Conventions
- Skill folder names and frontmatter `name` values use lowercase hyphen-case.
- Keep reusable skills generic: do not include private project names, local absolute paths, credentials, domains, or environment-specific identifiers.
- Each versioned skill should have a same-named command under [commands](../commands) when the workflow benefits from direct Claude Code invocation.
- Default output paths are in `/tmp` and frames are named `frame_%03d.jpg`; see [youtube-step-extractor/scripts/extract_frames.sh](../youtube-step-extractor/scripts/extract_frames.sh).
- Scripts default to `fps=1` and `max_height=720` to balance speed vs. detail; see [youtube-step-extractor/scripts/download_and_extract.sh](../youtube-step-extractor/scripts/download_and_extract.sh).

## Integration Points
- OpenSkills provides the repository-level installation path; Claude Code commands are installed separately from [commands](../commands).
- External tools vary by skill: `yt-dlp`, `ffmpeg`, `ffprobe`, and `bc` for video extraction; PHP/Docker and SPX for PHP profiling; target-project build/test/serve and browser audit tooling for web performance.

## Security
- Inputs include remote URLs and local paths; scripts write temporary videos to `/tmp` and clean them up; see [youtube-step-extractor/scripts/download_and_extract.sh](../youtube-step-extractor/scripts/download_and_extract.sh).
- Web performance changes must exclude private, session, personalized, checkout, account, and cookie-setting responses from shared caching or blind warmup.
