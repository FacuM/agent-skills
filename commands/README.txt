# Claude Code Commands

This directory contains one slash command for each versioned skill in the repository.

Install the complete command pack in a project:

  mkdir -p .claude/commands
  cp commands/*.md .claude/commands/

Install it for the current user instead:

  mkdir -p ~/.claude/commands
  cp commands/*.md ~/.claude/commands/

The Markdown filenames become command names:

- /knowledge-base-generator
- /mongodb-optimization-engineer
- /php-optimization-engineer
- /web-performance-optimizer
- /youtube-step-extractor

Install the corresponding skills as well. Each command names its matching skill so the agent can load the full workflow when invoked.

This file intentionally uses a .txt extension so Claude Code does not discover it as a slash command.
