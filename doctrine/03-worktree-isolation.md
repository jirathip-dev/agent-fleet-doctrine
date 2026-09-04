# 03 — Worktree Isolation

**One agent per worktree.** This is the safety boundary that makes parallel workers safe at all.

## The rule

Never have two agents editing the same files concurrently. Each worker — implementer or reviewer — gets its own isolated worktree. The orchestrator sits in the primary checkout; workers are never in the primary checkout's working tree.

```bash
# create a per-task worktree and branch
git worktree add ../<repo>-<task> -b feat/<task>
```

Or use the substrate's own worktree primitive (a multiplexer or tool may wrap this). The principle is identical: a separate checkout with its own branch that an agent can own outright.

## Why it is non-negotiable

- **No clobbering.** Two agents editing the same files overwrite each other's work. Git will not save you from a concurrent `write`; it only resolves *committed* conflicts, and agents often forget to commit.
- **Isolation is an execution boundary, not a hygiene nicety.** A worker with permission overrides ("run anything, no prompts") is dangerous only if it shares a directory with other work. Isolated to its own worktree, its worst case is contained.
- **Clean teardown.** When the task lands and merges, you remove the worktree. Its agent dies with it. No orphan agents, no half-edited primary checkout.

## Practice

- One worktree per task, per branch. Never reuse a worktree for a second task.
- Workers work only inside their worktree. The branch is theirs.
- The orchestrator works from the primary checkout on the integration branch — never from a feature worktree, which is removed after merge (and would kill it).
- Solve collisions by routing through the orchestrator, never by putting two agents in one worktree.
- Before removing a worktree that has uncommitted work, commit or push it first. Never delete real work.

## Cleanup when a worktree merges

Teardown is part of the task, not a forgotten afterthought.

- **After a successful merge, remove the task worktree** — which also removes/detaches the agent living in it (its pane is bound to that worktree). This is what prevents orphan agents and stale worktrees. Keep the primary checkout and its project workspace; remove task worktrees.
- **Never remove a worktree with uncommitted, unmerged work.** Merge or push first, then clean. Never delete real work.
- **Before cleanup, verify the merge actually landed**: the branch is an ancestor of the intended integration branch, the PR is closed, and the remote branch is gone. Verify from git, not from an agent's report.
- **Run cleanup deliberately, post-merge** — a cleanup that removes a worktree kills the agent in it. Preview with a dry-run first if your substrate supports one.
- **Orphaned agents with no worktree** (e.g. an orchestrator on a shared pane) get explicitly steered to done when their task lands.

## Permissive workers and the safety boundary

Workers often run with permission checks bypassed — no confirmation prompts — because that is the only way an autonomous agent finishes without constant human interruption. That is safe **only because of isolation, not despite it.** A worker that may run anything is dangerous only if it shares a directory with other work. Confined to its own worktree, its worst case is contained.

- The worktree is the **execution boundary**: workers may run anything inside their own worktree, and almost nothing outside it.
- **Never run two agents in the same worktree concurrently.** Isolation is the guardrail; two-on-one defeats it.
- A permissive worker is still **never authorized to merge, release, or rename**. GitHub mutations are the orchestrator's job (and the integration-to-production promotion/release is the human's).
- The permissiveness is configured in each worker's own settings, not passed per-start — the configs are the source of truth. Do not restate them as per-run flags.

## When it breaks

Scope leaks, not concurrency, are the usual failure. A worker that quietly edits files outside its worktree, or an orchestrator-style agent that spawns its work in the primary checkout, violates the boundary. Watch for it: a worker's diff should be limited to its own branch's worktree.

*Next: `04-adversarial-review.md`*
