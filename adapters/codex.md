# Codex adapter

Codex takes the doctrine's discipline via its CLI and its kickoff-brief mechanism. Model and role are separate axes: the model is selected by the fleet registry (respect the host's supported bare IDs for a given model family); the role is the behavioral boundary from the doctrine and the repo's role prompts.

## Where the role block goes

- **Role block FIRST, in the kickoff brief.** Codex does not have a system-prompt append flag, so the role boundary (implementer / adversarial reviewer / orchestrator) lives at the top of the kickoff brief, before the task. This is the single most reliable place to inject role behavior.
- Do **not** put role text in a repo-wide `AGENTS.md` — that file is shared context across every agent and would blur role boundaries.

## Kickoff mechanics

- **Post-spawn prompt is accepted.** Codex accepts a prompt sent after spawn: prompt the agent, then send an Enter/Return to submit it. This is the normal kickoff path.
- Prefer a brief **file** over a long inline prompt where possible: write the self-contained brief to a file, then prompt with a short pointer. Agents read better from a file (self-contained, replayable) and it keeps prompts small. Short inline prompts are fine.

## Skills directory

- Put `SKILL.md` in the tool's skill directory and reference it as `fleet-doctrine` to load the condensed doctrine.

## Worktree isolation

- One agent per worktree. Spawn implementers and reviewers in separate worktrees. The Codex CLI may run under an approval policy; worktree isolation is what keeps the granted permission safe.

## State / self-exit

- A Codex session can report `done` while a foreground process remains live and a branch lease lingers. A prompt to that dead session may sit in the queue unexecuted. Verify the actual process/state before concluding it finished, and route a missing/stale orchestrator through its canonical recovery rather than hand-restarting from a mid-continuation worker note.

## Provider/model specifics stay out of the doctrine

Provider-qualified model IDs can be rejected by a host's CLI at argv time even when the fleet registry prefers them. Verify the actual spawn argv accepts the bare ID. Keep all of this in the adapter; the doctrine chapters stay tool-agnostic.
