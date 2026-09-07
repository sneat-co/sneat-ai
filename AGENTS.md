# Agent instructions for sneat-ai

This is the public **skills-only** repository of Sneat.ai.

## Hard rule: no code

- Do NOT add CLI, backend or frontend code here — no Go modules, no npm
  packages, no application source. The only executable content allowed is a
  small script needed to install or validate the skills themselves.
- Backend logic belongs in `sneat-co/sneat-ai-backend`; CLI commands in
  `sneat-co/sneat-cli`; UI in `sneat-co/sneat-apps` / `sneat-co/sneat-ai-website`;
  wiring in `sneat-co/sneat-go`.
- Skills are thin: they teach an agent which `sneat` CLI commands to run and
  how to react to their JSON output. They never embed business rules that the
  backend validates; if the CLI lacks a capability, the skill reports the gap.

## Layout

- `skills/<name>/SKILL.md` — YAML frontmatter (`name`, `description`) + body.
- `skills/<name>/reference/*.md` — optional reference material loaded on demand.
- `.claude-plugin/` — Claude Code marketplace/plugin manifests (metadata only).

## Checks before a PR

- `claude plugin validate .` passes.
- Every command mentioned in a skill exists in the current `sneat` CLI
  (`sneat --help`, `sneat action --help`).
- Keep English and Russian examples in sync when a skill is bilingual.
