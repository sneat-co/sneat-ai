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
| [`sneat`](skills/sneat/SKILL.md) | Turn natural language (EN/RU/mixed) into Sneat actions through the Action Protocol (`sneat action new/add/commit`, `sneat context`, `sneat query`): shopping lists, schedules, birthdays — validated and committed by the Sneat backend. |
| [WB skill set](https://github.com/sneat-dev/wb/tree/main/ai/skills) | Use every public WB command through compact skills, then compose safe code changes and dependency campaigns with minimal duplicate CI. Canonical source lives in `sneat-dev/wb`; this repository only indexes it. |

## Installation (Claude Code)

Add the Sneat AI marketplace once, then install either plugin:

```text
/plugin marketplace add sneat-co/sneat-ai
/plugin install sneat@sneat-ai
/plugin install wb@sneat-ai
```

Claude namespaces the skills as `/sneat:sneat`, `/wb:wb-worktrees`,
`/wb:wb-deps`, and so on. Git-backed versions intentionally follow repository
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
and other compatible clients can install the canonical source directly:

- Sneat: `sneat-co/sneat-ai`, path `skills/sneat`
- WB: `sneat-dev/wb`, Codex plugin manifest at `.codex-plugin/plugin.json`,
  canonical skills at `ai/skills`

Claude-specific marketplace and plugin manifests are distribution metadata;
they do not contain a second copy of any skill.

## Repository scope rule

This repository is **skills only**. It MUST NOT contain CLI, backend or
frontend code. What lives here:

- `skills/<name>/SKILL.md` and their reference files (prompts, examples,
  schemas rendered for the model);
- plugin/marketplace manifests (`.claude-plugin/`) — distribution metadata;
- small agent-harness scripts strictly needed to install or validate the
  skills (e.g. a `claude plugin validate` helper).

Everything else has a home elsewhere:

| Concern | Repository |
|---|---|
| Sneat.ai backend — Action Protocol (`act_*`), semantic schema, validation, contact resolution | `sneat-co/sneat-ai-backend` (served on `api.sneat.cloud` as `/v0/sneatai/*`) |
| `sneat` CLI (`sneat action …`, `sneat context`, …) | `sneat-co/sneat-cli` |
| Web/mobile UI, the sneat.ai website | `sneat-co/sneat-apps`, `sneat-co/sneat-ai-website` |
| Composition root / wiring | `sneat-co/sneat-go` |

A skill that needs a capability the CLI does not have reports the gap; it never
re-implements it here.

## Design notes

- Skills follow the same layout as [SpecStudio skills](https://github.com/specscore/specstudio-skills):
  `skills/<name>/SKILL.md` with YAML frontmatter (`name`, `description`).
- Skills must stay thin: reuse the Typed Action Specification
  (`sneat convo actions --json`), the CLI, and the application facades.
- Future skills (candidates, not commitments): per-extension skills
  (`sneat-lists`, `sneat-calendar`) scoped like extension-specific bots;
  an MCP server generated from the same action specification.
