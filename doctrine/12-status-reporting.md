# 12 — Status Reporting

A status report is a **claim about state**, and like every other claim in the doctrine it has to be derived from ground truth, not from memory or from an agent's summary. A report that is wrong is worse than no report: the conductor steers on it, and a wrong "all green" tells the human nothing shipped that did not.

## The four buckets

Report progress in exactly four buckets, in this order:

1. **Integrated / shipped** — commits reachable from the remote integration branch, merged PRs, independently-verified CI.
2. **Produced, not integrated** — feature/review/orchestrator-branch commits; state committed/pushed/mergeable but not yet merged.
3. **In flight** — active orchestrator/reviewer/implementer lanes plus the current gate or blocker.
4. **Unverified / blockers** — worker claims, local-only changes, dirty checkouts, remote divergence, missing CI, review findings, pending human gates.

Bucketing forces honesty: a day can have many commits and zero merges. "We shipped a lot" and "we merged one thing" are different truths, and the buckets make that visible.

## Derive, don't compute

Never mentally compute the declared totals in a report. Derive them with commands:

- `git status --short --branch` — dirty checkouts, divergence.
- `git log` with an explicit counting scope — actual commit counts.
- `git branch --contains <sha>` — whether a change reached a given branch.
- the PR view + CI status — whether the head passed hosted checks.
- orchestrator/worker status + the latest pane tail — whether a lane is actually active.

Read these read-only. Treat every agent summary as a claim until verified. If you cannot verify a bucket, say it is unverified rather than filling it in.

## Presenting references

In user-facing status, prefer a direct, clickable link over a bare reference — a bare issue/PR number is ambiguous and unhandy for the reader. When in doubt, name the repo and the reference as a link, and never leave a bare `#N` that a reader could misread as belonging to a different project.

## The conductor's own report

The conductor applies the same discipline to itself. Its report of what the fleet did is a claim; it should be grounded in the same read-only commands, and it should not inflate "produced, not integrated" into "shipped."

*Next: `13-skill-selection.md`*
