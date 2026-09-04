# 05 — Gauntlet Loop

A **gauntlet loop** turns any goal into a builder-vs-critic loop against a real, fetchable bar. Use it when you want maximum quality, when the user says "loop until it beats X," or when a subjective judgment needs an objective comparison.

> **Attribution.** The gauntlet-loop technique is by **Matt Shumer** (Claude of Duty); the packaging into a reusable `gauntlet-loop` tool is by **robonuggets** (`robonuggets/gauntlet-loop`). This chapter describes the technique; it does not re-license away that attribution.

## The core moves

1. **Set the bar FIRST.** A real, named, fetchable, comparable reference: a live site, a named repo and its test suite, a published piece, a benchmark score, or a prior version you are trying to beat. Propose two or three candidate bars and let the user pick. **A vague bar is the single most common failure** — the critic invents a comparison and approves everything. No bar, no loop; you are just asking an agent to grade itself.

2. **Decompose.** Split the goal into the smallest pieces that can be judged on their own. Each piece is a single comparison.

3. **Per piece: fan out a builder and a SEPARATE critic with fresh context.** The critic inspects the actual output, puts it **next to the bar blind** (labels stripped so it cannot tell which is ours), and says which wins — then names the single biggest remaining gap. Harsh, binary A/B verdict, never a soft score (scores drift upward).

4. **Loop each piece until the critic picks ours blind.** The exit is *winning the comparison* or the human stopping — **never a fixed round count**. A fixed count means you ship whenever the timer runs out, not when it is actually good.

5. **Live progress.** Keep a visible progress surface (a status board, a markdown progress file, a digest) so the person can watch without being pestered.

## The convergence circuit breaker

A gauntlet loop can fail to converge — the critic keeps finding the same class of gap, each fix spawns a new round, and the loop has no natural end. It must stop and escalate; it must neither run forever nor ship on a timer.

- **Ordinary builder/critic rounds are bounded.** The bound is a **stop trigger**: when repeated non-convergence exhausts it, stop the loop and escalate — do not quietly start another round.
- **Escalation runs through one bounded recovery round** that is exceptional and separately authorized. It is not an automatic extension of the routine loop; whoever holds the authority for the loop grants it explicitly.
- **The human queue is for hard stops.** If the bounded recovery also fails to converge, the loop hard-stops to the human queue. Routine recovery — fixing a FAIL's findings and re-running the critic — never requires a human.
- **The cap is a stop trigger, never a ship timer.** Exhausting it never makes the critic pick ours, and it never replaces risk-specific judgment about whether a loop is converging.
- **Lineage follows the goal, not the artifacts.** Restarting the same goal under a new branch or PR does not reset the counter (see `15-review-convergence.md`).

## What breaks it

- **Vague bar** → the critic invents a comparison and approves everything.
- **The builder grading its own work** → the loop is no longer a loop, it's a monologue.
- **A soft critic** → everything passes, nothing improves.
- **Fixed round-count exit** → you ship on schedule, not on quality.
- **Over-specifying** → every extra instruction is one fewer judgment call the agent makes. Minimal instruction wins; let the bar do the judging.

## Who runs it

When the user asks for a gauntlet loop or says "orchestrator," the **orchestrator** runs the loop end-to-end and merges on pass — the conductor does not run the loop itself. The orchestrator's standing authority covers issue creation, branch push/merge, and worker fan-out, so no per-task approval is needed for the loop's internal steps. The conductor steps in only on a BLOCKED loop or a verdict failure. If the user did not ask for a loop, do not invent one.

## Portability

If the original tooling is unavailable, run the loop with whatever substrate you have: the implementer and the critic are separate agents in separate worktrees, and an orchestrator loops them until the critic's blind verdict flips. The doctrine (two-level orchestration, worktree isolation, adversarial review, verify-claims-not-summaries) is what makes the gauntlet portable.

*Next: `06-verify-claims-not-summaries.md`*
