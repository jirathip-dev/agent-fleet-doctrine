# Claude Code adapter

Claude Code takes the doctrine's discipline via its skill/CLI directory and its task-prompt mechanism. Model and role stay separate axes: the model is whatever the workflow selects; the role is the behavioral boundary defined by the doctrine and the repo's guidance.

## Where skills/context go

- **Skill directory.** Drop `SKILL.md` into the tool's skill directory so it can be referenced as `fleet-doctrine`. Skill dirs are the natural home for the condensed doctrine.
- **Role text belongs in the task prompt, not a repo-wide instruction file.** Append the role/context via the harness's append-system-prompt mechanism and put skills/context in the task prompt. Do **not** bake role text into `AGENTS.md` — that file is repo-wide and would leak role boundaries across every agent.

## Prompt mechanics

- Use the **append-system-prompt + task-prompt** path for the role and the brief. This keeps the role block distinct from the repo instructions.
- Pass acceptance criteria, the exact test/verify commands, and the output contract in the task prompt. The worker is self-contained; give it the whole brief, not a pointer it would have to reconstruct.
- One agent per worktree (see `03-worktree-isolation.md`). Spawn implementers and reviewers in separate worktrees; never two agents in one.

## Role/model/effort

- The harness (or fleet registry) selects the model. Keep it a separate field from the role. Expect a model change to affect new spawns, not a running session.

## Self-exit

Claude Code may ask to exit/navigate at first run; answer once per machine and then rely on the brief's output contract. A worker exits on done (artifact = proof). Remember the status-readout caveat: the label may say idle while the process is live — verify before reaping.

## Portability note

Keep the chapter wording in `../doctrine/` framework-agnostic. The tool-specific transport mechanics (role block placement, skill dir, prompt path) belong here in this adapter, not in the doctrine.
