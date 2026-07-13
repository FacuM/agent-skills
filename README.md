# Agent Skills Repository

A collection of reusable, task-focused skills for AI coding agents. Each skill lives in its own folder with a `SKILL.md` and, when needed, progressive-disclosure references or executable helpers.

## Available Skills

| Skill | Use it for |
| --- | --- |
| [`knowledge-base-generator`](knowledge-base-generator/SKILL.md) | Researching a recurring failure and recording the verified workaround in a project knowledge base. |
| [`php-optimization-engineer`](php-optimization-engineer/SKILL.md) | Profiling and optimizing PHP applications, database access, memory use, and runtime behavior. |
| [`web-performance-optimizer`](web-performance-optimizer/SKILL.md) | Running an end-to-end web performance loop: diagnosis, production-like baseline, safe optimization, remeasurement, and evidence handoff. |
| [`youtube-step-extractor`](youtube-step-extractor/SKILL.md) | Extracting frames from a YouTube video and turning the visual workflow into reproducible steps. |

## Install Skills

### OpenSkills (recommended for multiple agents)

[OpenSkills](https://github.com/numman-ali/openskills) installs `SKILL.md` packages for Claude Code and other agents that consume `AGENTS.md`.

Install from this repository and select the skills you need when prompted:

```bash
npx openskills install FacuM/agent-skills
npx openskills sync
```

For a shared, agent-neutral project installation, use universal mode:

```bash
npx openskills install FacuM/agent-skills --universal
npx openskills sync
```

To install only `web-performance-optimizer`, select that skill in the installer. OpenSkills installs project-local skills by default; add `--global` only when you intentionally want a user-wide installation.

### Manual Claude Code installation

Copy a skill folder into the target project's Claude Code skills directory:

```bash
mkdir -p /path/to/project/.claude/skills
cp -R web-performance-optimizer /path/to/project/.claude/skills/
```

Keep the complete folder together so `references/full-checklist.md`, `references/objective-loop.md`, and `references/evidence-template.md` remain available to the agent.

## Install Claude Code Commands

The [`commands/`](commands/) pack contains one slash command per versioned skill. From a clone of this repository, install the complete pack into a project:

```bash
mkdir -p /path/to/project/.claude/commands
cp commands/*.md /path/to/project/.claude/commands/
```

Or install the commands for the current user:

```bash
mkdir -p ~/.claude/commands
cp commands/*.md ~/.claude/commands/
```

Install the matching skills as well; each command delegates the detailed workflow to its named skill. See [`commands/README.txt`](commands/README.txt) for the command list.

## Use `web-performance-optimizer`

Mention the skill and provide the target page, URL, or flow plus any constraints:

```text
Use web-performance-optimizer on https://example.com/catalog.
Preserve the current UX, measure production-like artifacts, optimize the highest-impact bottleneck first, and return before/after evidence.
```

With the Claude Code command pack installed:

```text
/web-performance-optimizer https://example.com/catalog focus on mobile LCP and repeated public API requests
```

The skill guides the agent through:

1. repository and deployment intake;
2. request, cache, LCP, prerender, and image diagnosis;
3. a production-like baseline and comparison harness;
4. small, evidence-backed implementation cycles;
5. validation, rollback notes, and a reproducible final report.

It does not require a particular framework. The target project must provide its own build, test, serve, deployment, and audit tooling.

## Dependencies

| Skill | Runtime or integration requirements |
| --- | --- |
| `knowledge-base-generator` | A web- or GitHub-search capability for researching upstream issues. |
| `php-optimization-engineer` | A compatible PHP runtime or Docker environment; use SPX for runtime profiling unless it remains genuinely unavailable. |
| `web-performance-optimizer` | The target project's build/test/serve commands and a production-like browser or audit harness such as Lighthouse. |
| `youtube-step-extractor` | `yt-dlp`, `ffmpeg`, `ffprobe`, and `bc`; installation examples are in its `SKILL.md`. |

## Use Any Skill

1. Install the skill for the target agent.
2. Mention the skill name and provide its required input.
3. Let the agent read `SKILL.md` and only the supporting resources relevant to the task.
4. Review the agent's validation evidence before accepting code, configuration, or infrastructure changes.

## License

MIT. See [LICENSE](LICENSE).
