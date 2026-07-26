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
| [`wb-worktrees`](https://github.com/sneat-dev/wb/tree/main/ai/skills/wb-worktrees) | Create Git feature branches through WB in isolated central worktrees while canonical clones remain clean and current. Canonical source lives in `sneat-dev/wb`; this repository only indexes it. |

## Installation (Claude Code)

Add the Sneat AI marketplace once, then install either plugin:

```text
/plugin marketplace add sneat-co/sneat-ai
/plugin install sneat@sneat-ai
/plugin install wb@sneat-ai
```

Claude namespaces their skills as `/sneat:sneat` and
`/wb:wb-worktrees`. Git-backed versions intentionally follow repository
commits, so users receive updates without duplicated version metadata.

For local development, validate the marketplace and its local `sneat` plugin:

```bash
claude plugin validate .
```

The `sneat` plugin requires the `sneat` CLI on PATH (`go build ./cmd/sneat` in
[sneat-cli](https://github.com/sneat-co/sneat-cli)). The `wb` plugin requires a
current [`wb`](https://github.com/sneat-dev/wb) CLI on PATH.

## Installation (Codex and Agent Skills clients)

Both published skills use the portable Agent Skills `SKILL.md` format. Codex
and other compatible clients can install the canonical skill directory
directly:

- Sneat: `sneat-co/sneat-ai`, path `skills/sneat`
- WB worktrees: `sneat-dev/wb`, path `ai/skills/wb-worktrees`

Claude-specific marketplace and plugin manifests are distribution metadata;
they do not contain a second copy of either skill.

## Design notes

- Skills follow the same layout as [SpecStudio skills](https://github.com/specscore/specstudio-skills):
  `skills/<name>/SKILL.md` with YAML frontmatter (`name`, `description`).
- Skills must stay thin: reuse the Typed Action Specification
  (`sneat convo actions --json`), the CLI, and the application facades.
- Future skills (candidates, not commitments): per-extension skills
  (`sneat-lists`, `sneat-calendar`) scoped like extension-specific bots;
  an MCP server generated from the same action specification.
