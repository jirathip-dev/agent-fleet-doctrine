# 06 — Verify Claims, Not Summaries

A worker saying "tests pass" is a **claim**, not a fact. The doctrine's default posture toward any agent's self-report — including your own agents — is mistrust until you re-derive it from the ground truth.

## Why

Agents are unreliable narrators. They are also eager to please: a worker that genuinely believes it is done will report green even when it only checked in on a subcase, ran the wrong tool, or never actually ran the thing it claims to. A green subagent report is where fleets die quietly. So you verify.

## Verify these, concretely

1. **The diff.** Look at what actually changed, not what the agent *said* it changed. Check the exact files touched against the brief; flag scope creep and anything outside the task boundary.

2. **The exact runner, in the right lane.** A test can be written for one runner and accidentally invoked under another. A vitest file run under `node --test` crashes cryptically inside the test-framework's internals and *looks* like a genuine failure — when it is anything but. Before declaring a re-run RED, confirm you used the runner the file is written for. When unsure, run the repo's own script (`npm test`, `migrate`, the project's canonical check) rather than hand-picking one — the package scripts route each file to its correct lane.

3. **The exact head SHA.** Confirm you are verifying the commit the reviewer PASSed, not a neighbor. A pull request head and the branch tip are not always the same; the CI that ran and the code you merged must be the same commit. A stale installed bundle reporting an old test count is the classic trap.

4. **Per-unit deploy results.** A single "deploy-all green" can leave one unit on stale code. If the delivery is multi-unit (functions, apps, services), inspect the per-unit/per-function results, not just the aggregate job.

5. **The reviewer verdict and CI checks.** Independent review approved the final head, local gates passed at that head, and hosted CI passed. Each is a separate gate; one green does not imply the others.

## The right lane rule, restated

> Before declaring a re-run RED, confirm you used the runner the file is written for. A cryptic import-time crash inside a test framework's internals almost always means wrong-runner, not wrong-code.

## The delivery-verification bar

A green PR is not proof of a complete delivery. The full acceptance bar is:

1. independent review approved the **final head**;
2. local gates passed at that **exact head**;
3. hosted PR checks passed;
4. the merge landed on the **intended integration branch** (not a neighbor, not main by accident);
5. every post-merge deployment workflow passed, **including per-unit results** — a deploy-all job can report green while leaving one unit on stale code, so inspect per-function/per-app summaries, not just the aggregate;
6. the issue contains the evidence and the task worktrees are clean/removed;
7. no production/main promotion unless explicitly human-led.

Check `git branch --contains <sha>`, the merged PR's exact head, and per-unit deployment output — never an agent summary. If any deployment unit is still on an old version, keep the issue open and drive an additive corrective change through the same review/verify loop.

## The stale-bundle count check

Where a build/binaries product can silently run an old bundle (an iOS/Android build, a compiled binary, a cached build directory), a reviewer that predicts a specific test count gives you a cheap, decisive gate.

- Check out the **exact head** the reviewer PASSed (the PR head), not the branch tip.
- Regenerate the project if the tooling requires it, then run the **full** suite on a **clean** build directory — reusing the previous directory can silently run yesterday's binary.
- Assert the **exact predicted count** appears in the output, and that the specific new tests are named in the log. Matching the number proves the new tests were actually discovered and run, not a pre-fix bundle reporting an old count.
- A run that reports fewer than predicted means a stale bundle — treat it as a real blocker, not a discrepancy.
- Clean up the throwaway worktree and build directory after.

## When two reports contradict

Two delegated agents can reach opposite conclusions about the same system — one files a defect, a later run calls it "refuted." Do **not** average them, trust the newer one, or re-litigate in prose. Reproduce the specific claims yourself with a minimal, executed probe.

- Design the probe to distinguish the two claims in **one** run, and log the **raw observable** — every line, every frame, every count — not a pass/fail verdict.
- Run it against the **real source of truth** (the live daemon, the real wire bytes, the actual artifact), not against a re-summary of either agent.
- A run that admits "my first probe was buggy, corrected and re-ran" is a yellow flag on that agent's other conclusions — re-verify them, don't inherit them.
- "X is refuted" often means the two runs measured **different properties** and both were right. The probe should make both properties observable so they are not conflated.
- When a verification claim turns out to be the thing that failed (e.g. "build succeeded, N/N pass" on a bundle that was never exercised end-to-end), mandate a regression test that drives the **real path** and goes **red against current main** before it can certify green.

## Apply the same rigor to yourself

Your own orchestrator's self-report is also a claim. Re-run, re-check the commit, re-read the CI. The moment you start trusting your own summaries, the discipline erodes.

*Next: `07-self-exit-policy.md`*
