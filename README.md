# Agent Fleet Doctrine

**An opinionated operating manual for running fleets of coding agents.**

This is the *discipline layer* that sits on top of any agent grid — Claude Code, Codex, OpenCode, or a multiplexer such as Herdr. It exists because the dominant failure mode in multi-agent coding is not running the agents; it is **trusting them**. Agents are unreliable narrators of their own progress, so this doctrine installs a set of hard verification and lifecycle rules.

The doctrine is deliberately tool-agnostic. It does not depend on any specific multiplexer or CLI; the `adapters/` directory maps it onto specific tools.

## Why this exists

The individual techniques here are well known and scattered across docs and blog posts. What was missing was a single, coherent, portable operating doctrine that fuses them into one discipline — so that "run a fleet" means "run it *right*," not merely "run it."

## The doctrine, in one page

1. **Issue-first backlog.** The source of truth is the issue tracker. Every non-trivial task is an issue with a spec + acceptance criteria, gets progress comments, and is closed only with evidence — never a chat-only idea.
2. **Two-level orchestration.** One orchestrator per repo/workstream coordinates workers. Workers never self-integrate. A single integrator collects, reviews, runs gates, and merges once.
3. **Worktree isolation.** One agent per worktree. Never two agents editing the same files concurrently. Isolation is the safety boundary.
4. **Adversarial review.** Every change gets a fresh-context reviewer with a binary pass/fail verdict. The implementer never reviews its own work.
5. **Verify claims, not summaries.** A worker saying "tests pass" is a claim, not a fact. Check the diff, the exact runner, the commit, and the CI.
6. **Gauntlet loop.** Set a real, fetchable bar; run a builder against a harsh critic; compare blind; loop until ours wins. Never let the builder grade itself.
7. **Self-exit policy.** Workers exit when done (their artifact is the proof). The orchestrator never exits mid-batch; it stays alive through merge + cleanup.
8. **Merge gates.** Feature branches merge to the integration branch after review + CI. Promotion to production main is human-only.
9. **Docs sync.** Documentation is part of the task, not a follow-up. Update it in the same change.
10. **Issue hygiene.** File a new issue for every discovery. Never bury a finding in a PR body.
11. **Brief/manifest composition.** A self-contained brief is a contract; role, model, skills, brief, and harness are separate axes composed in a fixed order. Long briefs go in files, not inline prompts.
12. **Status reporting.** Report in four buckets — integrated/shipped, produced-not-integrated, in flight, unverified/blockers — and derive every total with a command, never from memory.
13. **Skill selection.** Worker context carries explicitly selected, narrow skills (a separate axis from role/model); role-gated, never blanket-injected.
14. **Merged-head cleanup.** A merged PR's head branch is deleted at merge time and verified gone; its lane workspace is removed deterministically too (local files archived with a manifest, credential-class files never silently destroyed, real uncommitted changes salvaged). Merged-branch rot is a hygiene failure, not an accepted backlog.
15. **Review convergence.** Quality/PASS is the exit condition; the round budget is only a stop-and-escalate circuit breaker, never a ship timer. Repeated non-convergence stops and escalates through one bounded, separately authorized recovery round to the human queue — routine recovery needs no human. Findings dispose as in-scope blockers (fix before merge), non-blocking follow-ups (filed as linked child issues via `Refs`), or rework/scope change (FAIL + rework). PASS + follow-up and FAIL + rework are distinct outcomes; a child issue never makes a failed parent mergeable, and lineage survives replacement branches/PRs. Merge is PASS-only: review PASS + green CI at the exact reviewed head.

## Install

As a skill (Claude Code / Codex / OpenCode / Herdr have skill directories):

```text
# put SKILL.md where the tool looks for skills
# e.g. <tool>/skills/, then reference "fleet-doctrine"
```

The full chapters live in `doctrine/`; `SKILL.md` is the condensed single-file version that is droppable into any agent context.

### Syncing a fleet from this repo

`bin/doctrine-sync` installs the public core (`SKILL.md` + `doctrine/` + `adapters/`) into a fleet's skill path, so the fleet pulls from this repo as the source of truth. It is **dry-run by default** and never destructive — run it with no flags to report the current upstream tag and any installed pin, then `--apply` to install. It compares the public release tag against the private overlay's `doctrine-pin` and surfaces an upgrade as a pending, testable step rather than a silent change. See `bin/doctrine-sync --help`.

## License

MIT. Techniques with external attribution (e.g. the gauntlet loop) retain their attribution in the relevant chapter; see `doctrine/05-gauntlet-loop.md`.

## Adapters

`adapters/` shows how to run this doctrine against a specific substrate. The Herdr adapter carries a note about AGPL interop: the doctrine does not incorporate Herdr source, it interoperates with it over an API. Host/project/model-policy specifics are never in this repo.

## The split rule (generic-core vs private overlay)

Everything here is public and portable. If a piece of content references a specific host, repo, merge-branch, project routing, provider/model policy, a local binary path, a fleet registry, a daemon's runtime config, or a per-incident post-mortem, it does **not** belong in this repo — it belongs in a private `fleet-operations` overlay. The test is simple: **"If a stranger with zero knowledge of my machines, repos, and provider could still use this sentence correctly, it's public. If not, it's overlay."**
