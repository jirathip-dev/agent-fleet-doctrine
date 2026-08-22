# OpenCode adapter

OpenCode takes the doctrine's discipline via its CLI and its start-argv kickoff. Model and role are separate axes: the model is selected by the fleet registry; the role is the behavioral boundary from the doctrine and the repo's role prompts.

## Kickoff: `--prompt` at start, not post-spawn paste

**The critical OpenCode mechanic is that the kickoff must go in the start argv (`--prompt`), not pasted after spawn.** Post-spawn pastes are often swallowed during OpenCode's TUI boot — a prompt typed too early lands in the TUI buffer and is never processed, so the agent just sits at the welcome screen. Put the kickoff inside the start argv:

```text
opencode ... --prompt "Read the brief and execute it fully."
```

This is the reliable path on OpenCode and the single most common cause of "every worker is idle at the TUI" failures.

## Where role/context goes

- **Role text in the kickoff `--prompt` or an attached context file — never in a repo-wide `AGENTS.md`.** The repo-wide file is shared across all agents and would blur role boundaries. Keep the role block scoped to the agent's own start argv or a per-agent context file.
- Put `SKILL.md` in the tool's skill directory and reference it as `fleet-doctrine` to load the condensed doctrine.

## Worktree isolation

- One agent per worktree; never two agents in one. OpenCode runs under a permissive permission config by default — worktree isolation is what keeps that safe (`03-worktree-isolation.md`).

## Model selection

- `--model` selects the engine. It is a separate axis from the role. A model change affects new spawns; it does not rebind a running session. Keep provider/model specifics here in the adapter, not in the doctrine chapters.

## State / self-exit

- OpenCode authors its own lifecycle state (it owns the native CLI path), so its state readout tends to be trustworthy. Workers exit on done (artifact = proof). Remember that the orchestrator never exits mid-batch — its stop condition keeps it alive through merge + cleanup.

## Scope discipline

Two parallel lanes on one repo are safe only on distinct branch namespaces, one agent per worktree, and with explicit branch-lease separation. The doctrine's worktree rule is what makes fan-out safe; the adapter is where the tool's branch-leasing mechanics live.
