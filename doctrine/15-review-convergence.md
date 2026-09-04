# 15 — Review convergence and follow-up semantics

Binary adversarial review (`04-adversarial-review.md`) and the gauntlet's
quality exit (`05-gauntlet-loop.md`) say when a change is good enough to PASS.
This chapter says what happens when a review/fix cycle does not converge, and
what a reviewer's findings mean once a verdict is out. **Quality stays the exit
condition; convergence has a circuit breaker.**

## PASS stays the exit condition

A review/fix cycle exits on quality, not on arithmetic. The change is done when
a fresh-context reviewer returns a binary **PASS** against the acceptance
criteria and the merge gates are green at the exact reviewed head
(`08-merge-gates.md`). A round counter never substitutes for that: the budget is
only a **stop-and-escalate circuit breaker** — a bounded number of rounds so
repeated non-convergence cannot burn forever. Exhausting the budget never ships
the change and never turns a FAIL into a PASS; it stops the loop and escalates.

The budget is risk-specific, not a fixed numeric cap on quality: choose the
bound for the work's risk, and escalate earlier when the failure pattern says
the loop is not converging. A number never replaces judgment about the specific
risk in front of you.

## Rounds, the recovery round, and the human queue

- **Ordinary review/fix rounds are bounded and routine.** A FAIL sends the
  change back for fixes; a fresh-context binary review repeats at the new head.
  Routine recovery — fixing a FAIL's findings and re-reviewing — proceeds
  without a human.
- **Repeated non-convergence stops and escalates.** When ordinary rounds
  exhaust their bound without converging (repeated FAILs, blockers that keep
  reappearing, the critic never satisfied), stop the loop. Do not quietly start
  another round; the cap is a stop trigger, not a ship timer.
- **One bounded recovery round is exceptional and separately authorized.**
  Recovery is not an automatic extension of the ordinary loop: whoever holds
  the authority for the work (an orchestrator's standing authority, or the
  human for a larger loop) must explicitly grant the single bounded recovery
  round before it runs.
- **The human queue is for hard stops only.** Conditions that exceed the
  bounded recovery — recovery also fails to converge, the work needs a scope
  change, or the risk itself needs a human decision — go to the human queue.
  Routine recovery never lands there; no human is required to make an ordinary
  fix round happen.

## Finding disposition

Every finding a reviewer raises gets one of three dispositions, and the verdict
states which (`04-adversarial-review.md`):

- **In-scope blocker** — the finding is inside the change's scope or
  acceptance criteria. It blocks merge: the verdict is FAIL until the blocker
  is fixed and the new head re-reviewed.
- **Non-blocking follow-up** — the finding is real but outside this change's
  bar, or merely surfaced by the work rather than required by its acceptance
  criteria. It does not block PASS, but it is not dropped: it is filed as a
  linked child issue and triaged on its own
  (`10-issue-hygiene.md`). The outcome is **PASS + non-blocking follow-up**.
- **Rework / scope change** — the change itself is pointed wrong, or the work
  outgrew its brief. The verdict is **FAIL + rework**: the change returns for
  rework, or the scope change goes to whoever authorizes it.

An undecided finding is treated as blocking: if a verdict does not classify a
finding as a non-blocking follow-up, it blocks the merge.

## PASS + follow-up versus FAIL + rework

These are distinct outcomes and must not blur into each other.

- **PASS + non-blocking follow-up** means the reviewed head is good and may
  merge when the gates are green; the filed child issue tracks the surfaced
  work for later.
- **FAIL + rework** means nothing merges. The change goes back (or is
  re-scoped), and the review/fix cycle continues from the FAIL.

A child follow-up issue **never** makes a failed parent mergeable. Merge
requires the parent's own binary PASS at the exact reviewed head plus green CI
(`08-merge-gates.md`). Filing a child issue is not a substitute for fixing a
blocker, and a PASS that carries follow-ups does not mean the follow-ups may be
ignored — they are filed, linked, and remain open until their own acceptance
criteria are met with evidence.

## Parent-open and lineage rules

- **Parent-open rule.** When the parent must stay open — because it cannot be
  auto-closed (downstream verification pending) or the child issue is merely
  surfaced by this work — link child issues with `Refs #N`, never
  `Fixes #N`/`Closes #N` (`08-merge-gates.md`). The parent closes only on its
  own evidence: binary PASS, green CI at the reviewed head, merged.
- **Lineage rule.** Review/fix rounds and their counter belong to the parent
  work, not to a branch or a PR. Replacing a branch or opening a replacement
  PR for the same parent does **not** reset the counter: the lineage travels
  with the parent, so the circuit breaker cannot be dodged by renaming the
  artifact. Child issues keep their `Refs` lineage to the parent; closing a
  child never closes the parent, and closing the parent never closes its
  children.

## Merge remains PASS-only

Integration merges require a binary review PASS plus green CI on the exact
reviewed head — always. A failed review may never merge. Rework and follow-ups
never bypass the merge gates: rework must re-PASS at a new head, and a follow-up
ships (if ever) only through its own reviewed, green PR. There is no FAIL-to-
merge path, with or without a child issue attached.
