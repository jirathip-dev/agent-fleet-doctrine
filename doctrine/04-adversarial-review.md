# 04 — Adversarial Review

Every change gets a **fresh-context reviewer** with a **binary PASS/FAIL verdict**. The implementer never reviews its own work.

## Why adversarial

The implementer has a goal and a bias: it wants the change to be good. It also has stale context — it knows what it *meant* to write, which makes it a poor judge of what it actually wrote. A reviewer with fresh context reads the change as a stranger would and finds what the author is blind to. The word *adversarial* matters: the reviewer is not a cheerleader, it is a hired skeptic looking for reasons to reject.

## Requirements

1. **Fresh context.** The reviewer must not share the implementer's history. It reads the diff, the brief, the acceptance criteria — then judges. No shared context carrying forward the implementer's assumptions.

2. **Binary verdict.** Not "looks good, maybe tweak this." A single, decisive **PASS** or **FAIL** against the acceptance bar. Vague feedback invites the implementer to rationalize; a binary verdict forces a position.

3. **The implementer never reviews its own work.** Self-review always under-weights its own blind spots. Route it to a separate reviewer every time — even (especially) when the author is confident.

## The residual-risk rule

A **good** reviewer will sometimes PASS while explicitly naming what it could not verify — for example:

> "PASS, with one residual gap: the full suite never ran at this tip. When it does, it should report N tests; a run reporting fewer means a stale installed bundle."

That qualification is **not noise to skip past**. It is a precise, cheap-to-close residual risk. **That gap must be closed, not inherited.** Before merging, the orchestrator (or the human) closes it: run the exact suite at the exact head, confirm the predicted count, and only then treat the PASS as complete.

Inheriting an honest reviewer's uncertainty into main is how unverified code ships. If the reviewer could not verify something, treat it as unverified until a concrete run closes it — not as a footnote.

## Output contract

Every review verdict should state clearly: the verdict (PASS/FAIL), the acceptance criteria checked against, what was actually run/verified (with the exact command runner), and any residual risk named explicitly. If a FAIL, say what must change.

*Next: `05-gauntlet-loop.md`*
