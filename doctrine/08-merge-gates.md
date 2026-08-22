# 08 — Merge Gates

Merges are layered. Each step has a gate, and the two most important gates — the integration gate and the production gate — are deliberately different.

## The layers

```
feature branch ──(review + CI + merge)──▶ integration branch
integration branch ──(HUMAN-ONLY ──▶ production / main
```

1. **Feature → integration.** A feature branch merges into the repo's integration branch *only after* independent review **and** hosted CI pass. This is the orchestrator's job and is a routine, gated step. The feature branch carries the change; the integration branch is where it proves itself.

2. **Integration → production/main.** Promotion to production main is **human-only**. No agent — orchestrator, worker, or conductor — promotes to production. This is the hard boundary between "the fleet shipped an integration" and "a human released it."

## The gates, concretely

- **All green, at one head.** Independent review approved the final head, local gates passed at that exact head, and hosted CI passed. A green PR body without these is a claim, not a merge warrant.
- **Merge to the intended branch.** Confirm the merge landed on the integration branch the work targets — not a neighbor, not main by accident. Check `git branch --contains` against the intended branch.
- **Do not promote main without a human.** The fleet may go as far as the integration branch. It never flips main on its own.

## The `Refs #N` vs `Fixes #N` auto-close gotcha

`Fixes #N` / `Closes #N` in a PR **auto-closes** issue N on merge — even an issue you intend to keep open for downstream verification (e.g. a fix that still needs on-device confirmation). This is a real problem: you merge a fix, and your hold-open issue silently closes.

Two guards:

- **If an issue must stay open past a merge, the PR must use `Refs #N`, never `Fixes`/`Closes #N`.**
- **Reopening an auto-closed issue is a mutation** — it needs the human's approval, not a unilateral agent action. If you see the mismatch, surface it, don't silently reopen.

## CI ownership

The **orchestrator** watches CI and merges when it is green — the conductor does not babysit CI runs. Orchestrator briefs must include: "wait for hosted CI to pass, then merge (squash); if CI fails, investigate and fix or stop + report." The conductor only checks back when the orchestrator reports done.

A CI run that is a **duplicate of the same head** (e.g. both a push-event and a pull_request-event run for one SHA) can produce a stuck/cancelled run that returns a transient failure while the real required checks are already green. Verify the merge is actually safe independent of the stuck run: confirm the required `pull_request` checks are `completed/success` and the PR's `mergeable` is `MERGEABLE`. If the only laggard is a duplicate run, that run is not a merge gate — do not let the orchestrator burn its whole loop retrying it.

## The approval gate (hard rule)

No mutations to GitHub state — repos, issues, PRs, merges, releases — without explicit human approval. Read-only discovery is always fine. Say what you are about to change and wait.

This gate governs the **orchestrator-side** (conductor, orchestrator CLI, `gh`). Workers may mutate issues as part of their briefed task (issue mutation is not a repo mutation subject to this gate), but they never merge, release, or rename.

A human saying "get this done" or "run the loop" is **not** an override of the approval gate. Promotion to production main is specifically human-only; no agent flips it.

## Scope discipline

A merge is the moment correctness is inherited by everyone. Never merge on a claim, never merge a scope leak, and never let a worker merge. The orchestrator merges to integration; the human promotes to production.

*Next: `09-docs-sync.md`*
