---
name: fleet-doctrine
description: "Use when running a fleet of coding agents. Opinionated operating discipline: issue-first backlog, two-level orchestration, worktree isolation, adversarial review, verify-claims-not-summaries, gauntlet loop, self-exit policy, merge gates, review convergence, docs sync, brief/manifest composition, status reporting. Framework-agnostic; tool mechanics live in adapters/."
version: 1.1.0
license: MIT
platforms: [macos, linux]
metadata:
  hermes:
    tags: [orchestration, fleet, multi-agent, worktree, review, verification, gauntlet, brief, manifest, status]
---

# Fleet Doctrine

Fleet orchestration discipline. Load this whenever you will run more than one coding agent on a task, or an orchestrator will coordinate workers.

## Core rules

1. **Issue-first.** Source of truth = the issue tracker. Spec + acceptance criteria in the issue. Progress comments as you work. Close only with evidence.
2. **Two-level orchestration.** One orchestrator per repo. It owns worker lifecycle, collection, gates, and the single merge. The conductor monitors the orchestrator only.
3. **Worktree isolation.** One agent per worktree. Never two agents in one worktree.
4. **Adversarial review.** Fresh-context reviewer, binary PASS/FAIL. Implementer never reviews its own change.
5. **Verify claims, not summaries.** Re-run the tests in the correct lane, check the exact commit/head, check CI. A green subagent report is a claim.
6. **Gauntlet loop.** Real, fetchable bar. Builder vs harsh critic, blind compare, loop until ours wins. Never a fixed round count.
7. **Self-exit.** Workers exit on done. Orchestrator stays alive until the batch's merge + cleanup completes.
8. **Merge gates.** Feature → the repo's policy-designated integration branch (which may be named `main`) after review + CI. The integration-to-production promotion/release is human-only.
9. **Docs sync.** Docs updated in the same task. `docs: updated` must appear in the verdict.
10. **Issue hygiene.** New issue for every discovery. `Refs #N`, not `Fixes #N`, when an issue must stay open past a merge.
11. **Brief/manifest composition.** Self-contained brief; role, model, skills, brief, harness are separate axes, composed in a fixed order. Long briefs go in files, not inline prompts.
12. **Status reporting.** Four buckets — integrated/shipped, produced-not-integrated, in flight, unverified/blockers. Derive totals with commands, never mentally.
13. **Skill selection.** Worker context carries explicitly selected, narrow skills (a separate axis from role/model). Role-gated; never blanket-injected; static default + named override; a lane that should have gotten a skill but didn't is a spawn defect.
14. **Merged-head cleanup.** A merged PR's head branch is deleted at merge time and verified gone; its lane workspace is removed deterministically after merge (local-only files archived with a manifest, credential-class files never silently destroyed, real uncommitted changes salvaged, never auto-deleted). Merged-branch rot is a hygiene failure, not an accepted backlog; sweeps repair but never replace the merge-time delete.
15. **Operator stop overrides everything.** A hard-stop / stand-down from the user ("stop all work now", "go idle", "pause", "do not spawn/push/merge") outranks any in-flight brief, even one issued in the same message. Comply immediately and in this exact shape: (a) do NOT start any mutating side effect not already running — no spawning workers, no pushes, no opening/merging/closing PRs, no issue writes; (b) leave worktrees, branches, and PRs exactly as-is — do not "tidy up", revert, or reset to reach a cleaner state, since freezing the current state IS the instruction; (c) do not begin executing a brief that arrived in the same message as the stop — the stop is the operative order; (d) confirm in one line what you did and did NOT touch, then stay idle until an explicit "resume". Ambiguity resolves toward stopping, not toward finishing "just this one step".
16. **Review convergence.** Quality/PASS is the exit condition; the round budget is only a stop-and-escalate circuit breaker, never a ship timer. Repeated non-convergence stops and escalates through one bounded, separately authorized recovery round to the human queue — routine recovery needs no human. Findings dispose as in-scope blockers (fix before merge), non-blocking follow-ups (filed as linked child issues via `Refs`, not `Fixes`), or rework/scope change (FAIL + rework). PASS + follow-up and FAIL + rework are distinct outcomes; a child issue never makes a failed parent mergeable; lineage travels with the parent, so replacement branches/PRs do not reset the counter. Merge is PASS-only: review PASS + green CI at the exact reviewed head.

## Boundaries

- The conductor may gather context, write the plan + brief, obtain approval for GitHub side effects, spawn/rearm the orchestrator, monitor it, and verify the final artifact.
- The conductor must NOT edit code, steer/stop workers directly, or create parallel worker lanes bypassing the orchestrator.
- Workers may mutate issues (as briefed) but NEVER merge/release/rename. Merges to the repo's integration branch are the orchestrator's job (whatever that branch is named), and the integration-to-production promotion/release is the human's.

## Adapters

- `adapters/herdr.md` — Herdr (public, AGPL-3.0 + commercial). A separate control-plane program (e.g. a fleet control plane) runs on top as a separate program over the plugin API (interop, not derivation).
- `adapters/claude-code.md`, `adapters/codex.md`, `adapters/opencode.md` — transport specifics; model and role are separate axes.

## Where tool-specific detail lives

The doctrine chapters stay framework-agnostic. The per-substrate mechanics (where the role block goes, how the kickoff is placed, how a fleet selects a model) live in `adapters/`, not in the chapters. Host/project/model-policy specifics are never in this repo at all.
