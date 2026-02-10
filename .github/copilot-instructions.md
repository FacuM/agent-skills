# Project Guidelines

## Code Style
- Shell scripts use `bash` with `set -e` and clear usage messages; keep that pattern in [youtube-step-extractor/scripts/download_and_extract.sh](youtube-step-extractor/scripts/download_and_extract.sh) and [youtube-step-extractor/scripts/extract_frames.sh](youtube-step-extractor/scripts/extract_frames.sh).
- Prefer explicit dependency checks (`command -v`) and informative error output before doing work.

## Architecture
- Single-skill folder layout: each skill has a `SKILL.md` and optional `scripts/` helpers; see [youtube-step-extractor/SKILL.md](youtube-step-extractor/SKILL.md).
- The YouTube step flow is: download with `yt-dlp` -> extract frames with `ffmpeg` -> analyze frames; scripts orchestrate this in [youtube-step-extractor/scripts/download_and_extract.sh](youtube-step-extractor/scripts/download_and_extract.sh).

## Build and Test
- No automated tests present.
- Manual run (Linux/macOS):
  - `./youtube-step-extractor/scripts/download_and_extract.sh "YOUTUBE_URL" /tmp/yt_frames 1`
  - `./youtube-step-extractor/scripts/extract_frames.sh /tmp/yt_video.mp4 /tmp/yt_frames 1`

## Project Conventions
- Default output paths are in `/tmp` and frames are named `frame_%03d.jpg`; see [youtube-step-extractor/scripts/extract_frames.sh](youtube-step-extractor/scripts/extract_frames.sh).
- Scripts default to `fps=1` and `max_height=720` to balance speed vs. detail; see [youtube-step-extractor/scripts/download_and_extract.sh](youtube-step-extractor/scripts/download_and_extract.sh).

## Integration Points
- External tools: `yt-dlp`, `ffmpeg`, `ffprobe`, and `bc` (used for frame estimate); see [youtube-step-extractor/scripts/extract_frames.sh](youtube-step-extractor/scripts/extract_frames.sh).

## Security
- Inputs include remote URLs and local paths; scripts write temporary videos to `/tmp` and clean them up; see [youtube-step-extractor/scripts/download_and_extract.sh](youtube-step-extractor/scripts/download_and_extract.sh).
