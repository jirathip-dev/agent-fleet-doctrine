# Herdr adapter

Herdr is a public multiplexer substrate that runs and coordinates agent sessions across panes, worktrees, and servers. This adapter maps the doctrine onto Herdr-style orchestration.

## Model and role are separate axes

- **Role** comes from the doctrine and the repo's role prompts (implementer, adversarial reviewer, orchestrator, conductor). It governs what the agent may do, not which engine runs it.
- **Model** is selected by the fleet registry / model map. It is independent of role. A reviewer model change affects *new* spawns only; it does not rebind a running session.

## Where the doctrine lands

- **Orchestrator** lives in the repo's primary checkout on the integration branch — never in a feature worktree (which is removed after merge and would kill it).
- **Workers** (implementers, reviewers) live in isolated worktrees, one agent per worktree. This is `03-worktree-isolation.md`.
- **Two-level orchestration** maps directly: the conductor monitors only the orchestrator; the orchestrator owns worker lifecycle, collection, gates, and the single merge.

## Spawn mechanics

- Spawn through the fleet gate, not raw agent start, when one exists. The gate handles capacity preflight, branch lease (the duplicate-work guard), and typed failures. Use a dry-run first to validate fleet, branch, and lease.
- A **branch lease** is the anti-duplicate guard: it refuses to start a second agent that would work on an already-leased branch. This is `worktree isolation` enforced by the substrate.
- **Kickoff placement matters.** Some backends accept a post-spawn prompt; others swallow pastes during TUI boot. On the latter, put the kickoff inside the start argv (`--prompt`) rather than pasting it after spawn. See the per-backend notes.

## Admission and capacity

- A capacity/admission refusal is a **real signal**, not noise. Record the exact arithmetic and stop retrying blindly. Never bypass the gate as a routine default; an override must be reported as an override.
- A `done`/`idle` label is **not** kill authorization. A live foreground process may be resumable even when the substrate says done. Verify process, cwd, pane, and task ownership before any reaping.

## State fidelity (a caveat)

Some backends (e.g. the major coding CLIs) surface state via *screen-scrape*, which can report `idle` while an agent is working and `working` after it paused. Treat the pane spinner and real output as the truth, and do not chase these mismatches as tooling defects. Where the substrate supports a native lifecycle-reporting hook, prefer the native authoring path and keep the sequence strictly increasing.

## AGPL interop note

Herdr is licensed **AGPL-3.0 (with a commercial dual)**. The doctrine does **not** incorporate Herdr source, and a separate control-plane program running on top of Herdr is a **separate program** that interoperates with Herdr over the plugin socket API. That is **interop, not derivation** — so the AGPL copyleft does **not** transfer to the doctrine or to a separately-licensed control plane. The three-way stack is clean only if the artifacts stay separate: the doctrine (MIT), the control-plane program (its own license), and Herdr (AGPL substrate). Do not copy Herdr source into either; interoperate over the API and keep the licenses distinct.
