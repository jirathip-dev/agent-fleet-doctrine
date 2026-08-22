# 01 — Issue-First Backlog

The issue tracker is the **source of truth** for work. Anything that is not an issue is not work — it is a thought, a chat aside, or a plan waiting to be wrong.

## Why

Multi-agent fleets fail when the only record of intent lives in a prompt, a chat thread, or an agent's head. A prompt is ephemeral; a chat thread is unreachable by the next worker; an agent's summary is a claim. An issue is a durable, queryable, reviewable artifact that any agent — or any human — can pick up later and know what was being attempted and why.

## Rules

1. **Durable work is an issue first.** A clearly-bounded problem, a spec, and acceptance criteria written *before* work starts. "Durable" means anything that touches behavior a user relies on, spans more than one file, spans sessions, or anything a fleet will pick up. **Explicitly out of scope for an issue:** throwaway experiments, one-off spikes, single-session/single-machine self-contained changes, and trivial edits — those may proceed without one. If a skip-worthy change grows into material/durable work, create or attach the issue before continuing. **Default template is LEAN:** a 3-line spec + 3 acceptance bullets beats a design doc for a small issue.

2. **Search before you create.** Duplicate issues are noise that splits evidence across two records. Search open issues before filing; if one already covers this, comment on it or extend it instead of duplicating.

3. **Progress comments as you work.** Comment when state materially changes: work started, first findings, verification evidence, a blocker, a PR opened. An issue is a conversation log, not just a tickle-file. A later reader should be able to reconstruct the story from the comments alone.

4. **Close only with evidence.** The issue may be closed only when its acceptance criteria are literally satisfied *and* the evidence is in the issue: test output, verification commands, a screenshot, a merged PR reference. "Done" without evidence is not done.

5. **Ground the spec in reality.** Before you write a spec for something that touches existing behavior, confirm the real schema/code you will change. Name the file and line or the data you are altering. A spec written from vibes makes the next agent re-discover everything from zero.

## The two lies to avoid

- **A closed issue without evidence is a lie.** It tells the next reader something shipped when it did not. The cost of a false "done" in a fleet is enormous because the next agent trusts the tracker.
- **An open issue that is actually done is noise.** It pollutes the backlog, starves triage, and makes the tracker untrustworthy in the other direction. If it is done, prove it and close it.

## Closing the loop

An issue is closed by the owner of the work once the acceptance bar is met and evidenced. If a reader later discovers a newly missing requirement, they file a *new* issue rather than reopening or silently mutating a closed one.

*Next: `02-two-level-orchestration.md`*
