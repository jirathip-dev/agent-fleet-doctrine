# AGENTS.md

This file tells agents operating *on this repository* how to treat it. This is not the doctrine — the doctrine lives in `doctrine/` and its condensed form in `SKILL.md`.

## What this repo is

A portable, framework-agnostic **operating manual** for running fleets of coding agents. It is documentation, not application code. There is no build, no runtime, and no test suite. Do not invent one.

## The one rule: keep it portable

Everything here is meant to be read by someone other than the author. Before you change anything:

- **No private identifiers.** No absolute paths to a specific machine, no personal repo names, no API keys, no certs, no provider-specific config.
- **No incident post-mortems.** Do not write "this broke when fleet X did Y." That belongs in a private log, not the doctrine. If a concrete failure taught you a general rule, state the *rule*, not the incident.
- **Framework-agnostic.** State the discipline; put tool-specific mechanics in `adapters/`.

## Where things go

- `doctrine/` — the chapters. Each is standalone, numbered, and self-contained.
- `adapters/` — how to run the doctrine on a specific substrate (herdr, claude-code, codex, opencode).
- `README.md` — public face and the one-page summary.
- `SKILL.md` — the installable, condensed doctrine for an agent skill directory.
- `LICENSE`, `CONTRIBUTING.md` — legal and process docs. Follow them.

## If you are an agent asked to edit this repo

- Read the relevant chapter or adapter before editing it.
- Make the smallest change that satisfies the request.
- Do not touch files outside the scope of the request.
- Do not add remotes, push, or create GitHub resources unless explicitly told to.
- Secrets and certs are gitignored; never add one.

## When a claim about the doctrine is uncertain

Treat a chapter's claims as normative *unless* you have a ground-truth reference to the contrary. Prefer to fix a chapter than to note a contradiction in a comment and leave it stale.
