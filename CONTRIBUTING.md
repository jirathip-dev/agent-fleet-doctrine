# Contributing

Thanks for contributing to the **Agent Fleet Doctrine**. This repo is a small, opinionated operating manual for running fleets of coding agents. Keep contributions in the same spirit.

## What kind of contribution fits here

- A new **doctrine chapter** under `doctrine/` (numbered, e.g. `11-...`).
- A new **adapter** under `adapters/` mapping the doctrine onto a specific substrate.
- A fix, clarification, or tightening of an existing chapter or adapter.
- A documentation correction in `README.md` or `SKILL.md`.

## Ground rules for content

1. **Framework-agnostic.** Chapters describe the discipline, not a specific vendor tool, model, provider, or API key. Concrete transport mechanics live in `adapters/`.
2. **Portable.** No private repo paths, no personal configuration, no credentials, no per-incident post-mortems of one team's fleet. Keep it applicable anywhere.
3. **Attribution stays.** If a technique originated with someone specific (e.g. the gauntlet loop → Matt Shumer / `robonuggets/gauntlet-loop`), keep that attribution in the chapter. Do not re-license it away.
4. **MIT, no AGPL-derived code.** Do not incorporate source from AGPL'd tools by copying it; document any interop relationship (see `adapters/herdr.md`).

## Adding a chapter

1. Pick the next number in `doctrine/`.
2. Write it as a standalone, self-contained document (1–3 pages) that reads on its own. Reference the one-page summary in `README.md` but don't assume the reader has it.
3. Keep the same title conventions and the same voice as the existing chapters.
4. Add a one-line entry in the `README.md` "doctrine in one page" summary so the chapter is reachable from the top.

## Adding an adapter

1. Add a file `adapters/<tool>.md`.
2. Describe how to run the doctrine on that substrate: where role/context goes (role block placement, `--prompt` at start vs. post-spawn paste), where skills live, and how model and role are separate axes.
3. Keep it about the tool, not any one user's fleet configuration.

## Process

- Open a PR with a clean description. Keep the change scoped to one concern.
- Style: plain markdown, short lines, headings for each idea, no fluff.
- CI is lightweight; a review that the content is factually accurate and portable is the gate.

## License

By contributing you agree your content is licensed under the MIT license (see `LICENSE`). Techniques with external attribution retain their attribution.
