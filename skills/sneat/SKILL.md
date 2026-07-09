---
name: sneat
description: |
  Operate a user's Sneat.app data (contacts, lists, calendar, spaces) from an
  AI agent by invoking the `sneat` CLI. Covers real backend operations
  (authenticated contact/space commands) and the conversational runtime's
  typed actions (`sneat convo ...`) for interpreting natural-language
  requests, inspecting the Typed Action Specification, and replaying
  conversations in a local sandbox. Trigger: "sneat", "/sneat", requests to
  manage the user's Sneat contacts, shopping lists, calendar or spaces.
---

# Sneat

Expose Sneat.app capabilities to AI agents through the `sneat` CLI. This
skill is a thin wrapper: all business logic lives in Sneat's application
facades behind the CLI — never re-implement it here.

```text
AI Agent → this skill → sneat-cli → Typed Sneat Actions → Application Facades → DALgo
```

## Prerequisites

The `sneat` CLI must be on PATH (build from `github.com/sneat-co/sneat-cli`
with `go build ./cmd/sneat`). For real-backend commands the user must be
signed in; check with `sneat whoami`. If not signed in, ask the user to run
`sneat auth login` themselves (interactive browser flow) — do not attempt to
capture their credentials.

## Real backend operations (authenticated)

These operate on the user's live data:

| Task | Command |
|---|---|
| Who am I / auth check | `sneat whoami` |
| List spaces | `sneat space list --json` |
| Select default space | `sneat space use <family\|private\|id>` |
| List contacts | `sneat contact list --json` |
| Get contact | `sneat contact get --id <id> --json` |
| Add contact | `sneat contact add --name "Jane Doe" --email jane@example.com --phone +353871234567` |
| Delete contact | `sneat contact delete --id <id>` |

Always pass `--json` when you need to parse output. Before deleting
anything, confirm with the user and echo exactly what will be deleted.

## Conversational runtime (typed actions)

The conversational runtime translates natural language into validated typed
actions executed through Sneat facades. The CLI runs it against an
**in-process sandbox** (in-memory datastore) — ideal for interpreting user
intent, testing prompts, and inspecting the action contract:

| Task | Command |
|---|---|
| Discover available typed actions | `sneat convo actions --json` |
| Actions of one extension | `sneat convo actions --scope listus --json` |
| Interpret a message into actions | `sneat convo say "buy milk and bread" --json` |
| Multi-turn conversation | `sneat convo say "add contact Jane Doe" "list my contacts" --json` |
| Auto-approve confirmations | `sneat convo say "delete contact Jane" --yes --json` |
| Replay a scripted conversation | `sneat convo replay conversation.txt --json` |

Replay file format: one message per line; a line that is exactly `yes` or
`no` approves/declines the pending confirmation; `#` starts a comment.

Scopes match Sneat extensions: `contactus` (contacts), `calendarius`
(calendar), `listus` (lists), `assetus` (assets). An extension-specific
context should pass only its own scope — e.g. in a lists-only context,
`sneat convo say "milk" --scope listus` interprets a bare item as
"add to groceries".

## Rules

- Destructive operations (delete contact/event, remove list items) require
  explicit user confirmation before you run them (or before passing `--yes`).
- Do not duplicate business logic: if a capability is missing from the CLI,
  report it as a gap instead of simulating it.
- Sandbox vs real: `sneat convo` writes to an in-memory sandbox only; the
  authenticated `sneat contact ...` commands write to the user's real data.
  Never present sandbox results as real changes.
