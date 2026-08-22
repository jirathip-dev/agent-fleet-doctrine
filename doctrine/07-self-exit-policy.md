# 07 — Self-Exit Policy

**Workers exit when they are done; the orchestrator never exits mid-batch.**

## Why split the rule

Agents are unreliable judges of "done." A worker that thinks it finished may have only partially landed the work — but its *artifact* (a branch, a diff, a verdict file) exists as a durable, reviewable proof, so the cost of it exiting is low: the orchestrator can look at the artifact and see the truth.

The orchestrator is different. Its job is to integrate: collect every worker, run the gates, push the branch, open the PR, wait for CI, merge, post issue evidence, and clean up. If it exits mid-batch — "done" after spawning the first wave but before collecting anything — the whole batch stalls silently. There is no artifact to inspect; the orchestration simply stopped.

## The rules

- **Workers exit on done.** When the deliverable is verified and its artifact exists (branch pushed, or verdict written), the worker exits. This frees the machine and signals completion. The orchestrator then verifies the artifact; the artifact, not the worker's word, is the proof.
- **An orchestrator never exits mid-batch.** Its stop condition keeps it alive through implementation → review → local gates → PR → hosted CI → merge → issue evidence → cleanup. It exits only when the batch's verdict gate passes. A sequence or an agent that stays alive until told otherwise is the correct posture.
- **The agent-reaper is the safety net.** For workers that hang, misjudge completion, or never exit, a reaper cleans up. It is a backstop, not a primary mechanism — the primary mechanism is the worker exiting on its own when its artifact exists.

## Pitfalls

- **Prompting a done orchestrator for new work.** A prompt may be accepted and the pane churn, but if the orchestrator is still driving a prior continuation, the new task queues behind it. It will not be immediate. Tell the waiting party the plan comes after the in-flight batch lands.
- **Confusing an idle label with an exit.** A status readout may say one thing while a live process remains. Verify the actual pane/process before concluding it is done — and never trust the label alone.
- **Never kill a working agent to make room.** If a worker is active, it is doing work. Make room by waiting or reaping genuinely done/hung agents, not by killing live ones.

*Next: `08-merge-gates.md`*
