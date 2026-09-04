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

- **All green, at one head.** Independent review returned a binary PASS on the final head, local gates passed at that exact head, and hosted CI passed. A green PR body without these is a claim, not a merge warrant.
- **Merge to the intended branch.** Confirm the merge landed on the integration branch the work targets — not a neighbor, not main by accident. Check `git branch --contains` against the intended branch.
- **Do not promote main without a human.** The fleet may go as far as the integration branch. It never flips main on its own.

## PASS-only merge

The integration merge requires a **binary review PASS plus green CI at the exact reviewed head** — nothing less, and no other route exists:

- **A failed review never merges.** There is no FAIL-to-merge path; rework must re-PASS at a new head before the change is mergeable again.
- **Follow-ups and rework never bypass the gates.** Filing a non-blocking follow-up issue does not make a FAILed change mergeable, and neither does returning the change for rework. A **child follow-up issue never makes a failed parent mergeable** — the parent head must itself PASS and be green (see `15-review-convergence.md`).
- **Follow-ups ship on their own warrant.** A child issue attaches to the parent work with `Refs #N` and ships only through its own reviewed, green PR — never piggybacked on the parent's merge.

## The `Refs #N` vs `Fixes #N` auto-close gotcha

`Fixes #N` / `Closes #N` in a PR **auto-closes** issue N on merge — even an issue you intend to keep open for downstream verification (e.g. a fix that still needs on-device confirmation). This is a real problem: you merge a fix, and your hold-open issue silently closes.

Two guards:

- **If an issue must stay open past a merge, the PR must use `Refs #N`, never `Fixes`/`Closes #N`.**
- **Reopening an auto-closed issue is a mutation** — it needs the human's approval, not a unilateral agent action. If you see the mismatch, surface it, don't silently reopen.

## CI ownership

The **orchestrator** watches CI and merges when it is green — the conductor does not babysit CI runs. Orchestrator briefs must include: "wait for hosted CI to pass, then merge (squash); if CI fails, investigate and fix or stop + report." The conductor only checks back when the orchestrator reports done.

A CI run that is a **duplicate of the same head** (e.g. both a push-event and a pull_request-event run for one SHA) can produce a stuck/cancelled run that returns a transient failure while the real required checks are already green. Verify the merge is actually safe independent of the stuck run: confirm the required `pull_request` checks are `completed/success` and the PR's `mergeable` is `MERGEABLE`. If the only laggard is a duplicate run, that run is not a merge gate — do not let the orchestrator burn its whole loop retrying it.

## The approval gate (standing authority)

The orchestrator holds a **standing authority** for its briefed loop's internal steps: issue mutation as briefed, pushes of its own integration/promotion branches, and the merge of a reviewed, CI-green PR to the integration branch. No per-PR human approval is required for integration-branch merges — asking per PR is the wrong default. When the merge warrant holds (all green at one head, correct integration branch), the orchestrator merges and reports.

The human gate remains for: **promotion to production main** (no agent flips it), spend, destructive operations, scope changes, and any GitHub mutation outside the orchestrator's briefed loop.

Workers never merge, release, or rename; issue mutation inside a worker's briefed task is fine. Read-only discovery is always fine. "Get this done" or "run the loop" is not a license to skip the human gate above.

## Delivery contract — who pushes what

- **The worker pushes its own branch.** After gates pass it commits, pushes `origin/<branch>`, and reports the exact SHA. A local-only committed head is **not delivered**: treat an idle/done worker with an unpushed head as a stalled delivery — prompt it once to push, escalate if it repeats.
- **The orchestrator owns**: opening the PR, spawning the fresh reviewer, waiting on hosted CI, and merging a reviewed, green PR to integration. It never waits for its own push of worker work — it waits on the worker's pushed branch with event-driven waits (`agent wait`, `gh pr checks --watch`); fixed long sleep loops are banned.
- **Reviewers are read-only**: no commits, no pushes, no merges.
- Exception: documented mechanical pushes (report-only commits, promotion branches) belong to the orchestrator, not to workers.

## Scope discipline

A merge is the moment correctness is inherited by everyone. Never merge on a claim, never merge a scope leak, and never let a worker merge. The orchestrator merges to integration; the human promotes to production.

*Next: `09-docs-sync.md`*
