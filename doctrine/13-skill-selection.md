# 13 — Skill selection (worker context)

Briefs and task manifests carry **explicitly selected skills**. Skill is a
separate axis from role and model — it is narrow, deliberately chosen, and never
blanket-injected.

## Rules

- **Explicitly selected, narrow.** Attach the best-fit skill(s) for the lane and
  task type. Do not inject every skill in the registry, and do not stack
  irrelevant skills.
- **Role-gated.** Reviewers do not get write-capable implementation skills;
  implementers do not get orchestration skills. Compatibility is enforced at
  resolution, not implied.
- **Resolve, don't guess.** Skill names must resolve against the registry; an
  unknown or duplicate name is a resolution failure, not a warning.
- **Static default + narrow override.** Most tasks use a per-fleet/per-lane
  default; deviate only with a named, justified override. Keep the default map
  explicit and review it periodically.
- **Provenance.** Record role/model/skill names, brief path/hash, harness. Verify
  a spawn's composed prompt carried the intended skills (dry-run + catalog read-
  back) — never trust an agent's claim that it read one.

## Composition order

```
role base → selected skill guidance → repo instructions → issue/acceptance → brief
```

## Missing injection is a failure

A lane that was supposed to receive a skill but did not is a spawn defect — no
silent fallback to "no skills". The composed context is the contract; verify it,
not the agent's self-report.
