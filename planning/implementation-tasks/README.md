# Implementation task queue

Deferred **code** work captured during planning sessions. Not a substitute for feature specs (`docs/features/`) or sprint YAML—those stay higher level until you promote tasks.

## When to add a file

- Brainstorming concluded with clear engineering steps but implementation is **not** happening in this chat.
- Spec was updated and you want a checklist for a future `build it` session.

## Naming

`YYYY-MM-DD-<short-slug>.md` (e.g. `2026-09-29-auth-session-refresh.md`)

## Template

Start from [`_template.md`](./_template.md).

## Lifecycle

| Status | Meaning |
|--------|---------|
| `proposed` | Captured from discussion; may change |
| `ready` | Spec aligned; safe to implement |
| `in_sprint` | Linked sprint task(s) exist in client/server `current.yaml` |
| `done` | Implemented; archive or delete after merge |
