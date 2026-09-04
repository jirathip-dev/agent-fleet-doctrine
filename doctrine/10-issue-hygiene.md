# 10 — Issue Hygiene

The issue tracker is the **project log**. Treat it with the same care you treat the code: every discovery that matters gets filed, cross-linked, and triaged — and never buried.

## The rules

1. **Discover an issue → file it.** Bugs, flaky tests, tech debt, design questions, and follow-ups found while working all become issues. If you find a real problem and do not file it, you have decided it is not worth fixing — and you may be the only one who ever knew about it.

2. **Never bury a finding in a PR body or a chat.** A finding in a PR body is invisible to triage; a finding in a chat is invisible to everyone who was not online. File the issue, link it, leave it open for triage. A good default is that a PR closes one issue and references any others it merely surfaced.

3. **Cross-link with `Refs #N`.** When a PR or a comment relates to an issue without satisfying it, reference it with `Refs #N`. Use `Closes #N` / `Fixes #N` only when the PR actually satisfies the issue — and only when you want it auto-closed (see `08-merge-gates.md` for the hold-open gotcha).

4. **Comment progress.** Issues carry a living narrative. Comment on the issue when state materially changes: work started, findings, verification evidence, blockers, PR opened. A reviewer or later worker should be able to reconstruct the story from the issue alone.

5. **By default only the owner's orchestrator closes an issue.** Do not close an issue that is not yours unless explicitly asked. The orchestrator that owns the work closes it, with evidence. A worker may comment and mutate issues as briefed, but closing is the owner's call.

## Who owns issue discipline

The **orchestrator** reviews its workers' issue hygiene as part of its verdict gate: did the worker file issues for its discoveries, comment its progress, and close with evidence? A worker that went quiet about findings is a review finding, not a cleanup chore.

Workers may use the issue-mutation tooling (create/comment/close) as part of their briefed task — issue mutation is **not** a repo mutation subject to the approval gate (only merges, releases, and renames are gated). If a worker is not allowed to touch issues, the orchestrator files and updates them on its behalf. Never close an issue you do not own unless explicitly asked; the owner's orchestrator closes it.

## The two failure modes

- **Burying a discovery** in a PR or chat → the finding is lost; the bug ships again next month.
- **Closing without evidence, or partially closing** → the tracker lies. A reader trusts the tracker, so a false close propagates the lie to every downstream agent.

## Close only with evidence

Reinforcing `01-issue-first-backlog.md`: an issue is closed when its acceptance criteria are met **and** the evidence is in the issue — test output, verification commands, screenshots, a merged PR reference. Otherwise keep it open and say what remains.

## Parent/child follow-ups, rework, and lineage

- **Follow-ups are children.** A non-blocking finding from review is filed as a child issue of the change's parent issue (see `04-adversarial-review.md`, `15-review-convergence.md`), with its own spec and acceptance criteria. Link it with `Refs #N` — never `Fixes #N` / `Closes #N` — when the parent must stay open or the issue is merely surfaced by the work (the hold-open rule, `08-merge-gates.md`).
- **Group related findings deliberately.** Findings that share one fix or one acceptance bar may be grouped into a single child issue; unrelated findings get separate issues. Search before filing (`01-issue-first-backlog.md`).
- **Rework is not a follow-up.** A FAIL + rework stays on the parent; the finding is not refiled as a new child issue to clear the FAIL. The parent continues its own lineage until it PASSes at the reviewed head and merges — or the scope change is authorized separately.
- **Lineage survives replacement.** The round counter and the review history belong to the parent issue, not to a branch or a PR. Closing a PR, deleting a branch, or opening a replacement PR for the same parent does not reset convergence state (`15-review-convergence.md`). Child issues keep their `Refs` lineage to the parent.
- **Closing stays per-issue and evidence-only.** A child closes when its own acceptance criteria are met with evidence. A parent closes only on its own evidence — binary PASS, green CI at the reviewed head, merged — never because its child issues were filed or closed.

## A practical rule of thumb

An issue for an existing behavior should name the file:line (or the data/call-path) of the thing being changed *before* you file it. That makes the downstream fix scoped and trustable, and it forces you to actually understand the problem instead of describing a symptom.

*Next: `11-brief-and-manifest-composition.md`*
