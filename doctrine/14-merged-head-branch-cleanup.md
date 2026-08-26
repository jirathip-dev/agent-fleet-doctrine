# 14 — Merged head-branch cleanup (no merged-branch rot)

When a PR merges, its head branch is dead weight. Delete it **at merge time**,
and verify the deletion. A merged branch left behind is not harmless clutter:
it pollutes the remote refs, confuses cleanup tooling, and every subsequent
`ls-remote`/branch sweep has to re-derive what the merge step should have done.

## Rules

- **Delete on merge, not later.** The merge step itself removes the head branch
  (merge-tool flag, or delete the local branch after a local merge). "Later" is
  a backlog, and backlogs rot.
- **Verify, don't assume.** After merge + delete, confirm the ref is gone
  (`ls-remote --heads` or the tool's own read-back). "It probably deleted" is
  not verification; a silently surviving branch is a defect, not a detail.
- **Only merged heads are candidates.** A branch whose PR is open, or not yet
  merged, is work in flight — never rot. Do not confuse the two.
- **Integration branches are never candidates.** Long-lived refs
  (`main`, `master`, `staging`, `develop`, `gh-pages`, …) are protected by
  name, in every sweep.
- **Scheduled sweeps are a safety net, not the rule.** A periodic sweep may
  reconcile drift, but it repairs the failure of merge-time deletes; it does
  not replace them.

## Signals that drift already happened

- Remote head refs that correspond to PRs closed as merged.
- Cleanup/reaper passes reporting "merged but still present" lanes.
- Branch listings where every ephemeral PR branch has a twin still on origin.

## Verification

- After every merge: the head ref is absent from the remote refs.
- In a well-operated grid, cleanup passes find zero merged-but-present
  branches; any hits are noise from a merge-time delete that failed and get
  fixed by restoring the merge-time delete, not by sweeping forever.
