# 02 — Two-Level Orchestration

Running many agents is not a flat pool of workers each talking to the same repo. It is a strict two-level structure: a **conductor** on top, one **orchestrator** per repo or workstream, and underneath it a set of **workers**.

```
conductor
   └── orchestrator (one per repo/workstream)
          ├── worker (implementer)
          ├── worker (implementer)
          └── worker (adversarial reviewer)
```

## The three roles

- **Conductor.** The outer layer. It gathers context, writes the plan and the brief, obtains approval for any GitHub side effects, spawns or re-arms the orchestrator, monitors the orchestrator's status, and independently verifies the final artifact. It does **not** edit code, steer workers, or create parallel lanes that bypass the orchestrator.

- **Orchestrator.** One per repo or workstream, owned by the workstream, sitting in the primary checkout on the integration branch. It is the **single integrator**. It owns the entire worker lifecycle: resolving the brief, fanning out workers into isolated worktrees, watching their status, collecting their artifacts, running the gates, opening the PR, waiting for CI, merging once, and cleaning up. Workers never self-integrate.

- **Worker.** An implementer or reviewer in its own isolated worktree. It does exactly what its brief says, produces an artifact, and exits. It may mutate issues as briefed, but it never merges, releases, or renames. Merges are the orchestrator's job; promotion to production main is the human's.

## Why two levels, not one

A flat pool has no single owner of "did this actually land?" Each worker can report green and walk away; the integration decision is nobody's. The orchestrator collapses that decision into one accountable agent whose **stop condition keeps it alive** through implementation → review → local gates → PR → hosted CI → merge → issue evidence → cleanup. The conductor, in turn, monitors *only* the orchestrator — not the workers — so its own context stays small and its attention stays on the one thing that can go wrong: the integrator.

## Boundaries (hard)

- The conductor may gather context, write plans and briefs, obtain approval, spawn/rearm the orchestrator, monitor it, and verify the artifact. It must **not** edit product code, prompt or stop workers directly, or bypass the orchestrator with its own worker lane.
- The orchestrator owns worker lifecycle and the single merge. It stays alive through the whole batch.
- Workers may touch issues but never merge, release, or rename. They self-exit when done.

## What it buys you

- A single accountable integrator means there is always one agent that knows whether the work actually landed and can defend it with evidence.
- The conductor stays cheap: it watches one orchestrator, not N workers.
- Isolation (next chapter) is what makes fan-out safe; this chapter is why there is always one agent collecting it.

## One vs many workers

The orchestrator decides how many workers to fan out, and the answer changes everything.

- **One worker** — for a single tightly-coupled module, one coherent shape. Never split a coherent module across two agents.
- **Many workers in parallel** — the default for decomposed work. Split the work into the **maximum** number of genuinely independent tasks, then fan out one implementer per task into its own isolated worktree, and one fresh-context adversarial reviewer per task (separate worktree, fresh context). The orchestrator stays the single integrator: collect all, review all, run all gates, merge once.
- Natural splits are the seams of the domain: a git-poller ∥ an API-watcher ∥ a schema-extractor; a frontend ∥ a backend; one per language or per subsystem. If two pieces touch the same files, they are not independent — do not split them.

**Never let two agents edit the same files concurrently.** That is not a parallelization failure, it is a design failure. Worktree isolation is the rule; conflicts resolve through the orchestrator.

## The generic delegation protocol

The orchestrator drives a worker through the same sequence every time, regardless of substrate:

1. **Create the worktree** from the integration checkout, on the task's own branch. Record the pane/agent handle the substrate returns.
2. **Write the brief to a file** in the worktree root — never paste a long brief as an inline prompt argument (see `11-brief-and-manifest-composition.md`). Prompt with a short pointer to the file.
3. **Spawn the worker** with the kickoff in the substrate's reliable path (some substrates accept a post-spawn prompt; others swallow pastes during boot — put the kickoff in the start argv).
4. **Watch** the worker until it idles: check status and read its terminal output. Idle is a claim; the output is evidence.
5. **Collect** the worker's artifact (its final summary in the terminal, plus the worktree diff) and **verify the claims yourself** — rerun the exact tests in the correct lane (see `06-verify-claims-not-summaries.md`).
6. **Route to a fresh-context adversarial reviewer** in a new worktree off the same branch, with no shared history with the implementer. Never let the implementer review its own work.

*Next: `03-worktree-isolation.md`*
