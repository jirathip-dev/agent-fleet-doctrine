# 09 — Docs Sync

Documentation is **part of the task**, not a follow-up. If a change alters behavior, the docs that describe that behavior are updated in the **same** change — same worktree, same PR.

## Why

A fleet that defers docs creates a lag that compounds: the code changes, the docs go stale, the next agent reads the stale docs and reinvents (or undoes) the work, and the staleness cascades. Keeping docs in the same change means a reader can trust that what is documented is what the code does.

## The rule

Every task that touches code, an API, configuration, a CLI, an operation, or user-visible behavior must, in the same change:

1. **Inspect** the docs that describe the touched behavior — at minimum the project's README, its architecture/API/operations docs, and its agent-guidance file when relevant.
2. **Update** them to match the new behavior when they are now wrong.
3. **Remove stale claims** rather than documenting a deleted feature. Leaving a stale paragraph that describes removed behavior is not "keeping docs" — it is actively misleading the next reader.

## The verdict contract

Every orchestrator brief and final verdict must state, explicitly:

- `docs: updated` — with the paths changed, **or**
- `docs: checked — no change needed` — with the reason.

A worker may **not** declare a task complete while it leaves a known code/documentation mismatch. If the correct documentation update is genuinely outside the task boundary, file a follow-up issue before reporting completion — never just mention it in a chat.

## Attention nuances

- When a recurring workflow or tool behavior changes, update the durable fleet/skill guidance as well as the project-local docs. Skills are the long-lived instruction set; leave them accurate.
- "I'll do docs later" is a failure mode. Docs later are usually docs never.

*Next: `10-issue-hygiene.md`*
