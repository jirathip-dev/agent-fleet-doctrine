# 11 — Brief and Manifest Composition

A worker must know everything it needs from the brief and nothing it needs to guess. The brief is the worker's sole context; the manifest decides how it is assembled. Getting this right is what lets an agent run self-contained and be trusted to self-exit.

## The brief, as a contract

A self-contained brief is a contract, not a description. It should have, explicitly:

```
Goal: <one sentence + acceptance criteria>
Context: <paths, stack, conventions, relevant files>
Constraints: <no new deps without approval, keep scope, don't touch X>
Commands: <exact test/lint/verify commands + expected output>
Output contract: <files changed, test results, commit message>
Exit on done: when the deliverable is verified (branch/PR pushed or verdict
written), exit. Never exit mid-task — if unsure you're done, stay alive and say
so. The orchestrator verifies; your artifact is the proof.
```

A brief is written for a worker that knows **nothing else** — not the conversation, not the plan, not the repo's history. If the worker would have to reconstruct context from the chat, the brief is incomplete.

## The brief-file rule

**Never paste a long brief as an inline prompt argument.** Write it to a file, then prompt with a short pointer. Reasons:

1. Long inline prompts can trip input guards (false positives on certain phrases in long text) and get blocked.
2. Agents read far better from a file: self-contained, replayable, and audit-able.

Pattern (conducted by the orchestrator): write a `.brief.md` into the worktree root or a known temp path, then prompt the worker with a short pointer — `Read <path> and execute it fully.` Short inline prompts are fine; long briefs go in files.

## The manifest: separate axes

A worker's context is composed from distinct, deliberately-separate axes. Model is not role; skills are not role; harness is not policy.

- **Role** — the behavioral boundary (implementer, adversarial reviewer, orchestrator, conductor). Defined by the doctrine and the repo's role prompts.
- **Model** — the engine the role runs on, selected by the fleet registry / model map. Independent of role. A model change affects *new* spawns only; it does not rebind a running session.
- **Skills** — the narrowly-selected capability set the role may lean on. Explicitly selected, never an automatic all-skills dump.
- **Brief** — the issue-derived task: specs, acceptance criteria, constraints.
- **Harness** — the transport only. It must not choose policy, role, or model.

Because these are separate axes, changing one must not silently change the others. A reviewer is not "a model you point at the diff" — it is a role with a boundary, running on a selected model, with an explicit skill set.

## Composition order

Assemble the manifest in this order, and record provenance (which role, model, skills, brief path/hash, harness were used):

```
role base → skill guidance → repo instructions → issue/acceptance → brief
```

The **role base** goes first because it defines the boundary everything else runs inside. **Repo instructions** are the durable per-repo context (structure, conventions, local tooling). **Issue/acceptance** is the target. The **brief** is the task-specific mandate. A missing role injection is a **spawn failure**, not a warning — verify it via dry-run and output read-back, because an agent's claim that it "read a role" is not proof.

## Adapter placement (kept out of the doctrine)

Where the role block lands is a harness concern, not doctrine. Some harnesses take a system-prompt append; some require the role block first in the kickoff brief; some take it in the start argv. That belongs in `../adapters/`. The doctrine only fixes the composition order and the axes.

*Next: `12-status-reporting.md`*
