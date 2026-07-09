# sneat-ai

AI skills for [Sneat.app](https://sneat.app).

These skills are **thin integrations** that expose Sneat capabilities to
external AI agents (Claude Code, future ChatGPT integrations, coding and
reasoning agents) by invoking [`sneat-cli`](https://github.com/sneat-co/sneat-cli).
They contain no business logic — everything goes through the CLI into the
Typed Sneat Actions and application facades:

```text
Claude Code / AI Agent
        │
        ▼
  Sneat AI Skill        (this repo — skills/<name>/SKILL.md)
        │
        ▼
    sneat-cli
        │
        ▼
Typed Sneat Actions     (conversational runtime action specification)
        │
        ▼
Application Facades
        │
        ▼
  DALgo Datastore
```

## Skills

| Skill | Purpose |
|---|---|
| [`sneat`](skills/sneat/SKILL.md) | Operate contacts, lists, calendar and spaces via `sneat-cli`; interpret natural language through the conversational runtime (`sneat convo`). |

## Installation (Claude Code)

Copy (or symlink) a skill directory into your project's `.claude/skills/`
directory, or install this repo as a plugin/marketplace once published:

```bash
mkdir -p .claude/skills
cp -r skills/sneat .claude/skills/
```

Prerequisite: the `sneat` CLI on PATH (`go build ./cmd/sneat` in
[sneat-cli](https://github.com/sneat-co/sneat-cli)).

## Design notes

- Skills follow the same layout as [SpecStudio skills](https://github.com/specscore/specstudio-skills):
  `skills/<name>/SKILL.md` with YAML frontmatter (`name`, `description`).
- Skills must stay thin: reuse the Typed Action Specification
  (`sneat convo actions --json`), the CLI, and the application facades.
- Future skills (candidates, not commitments): per-extension skills
  (`sneat-lists`, `sneat-calendar`) scoped like extension-specific bots;
  an MCP server generated from the same action specification.
