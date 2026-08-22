# Adapters

This directory maps the doctrine (see `../doctrine/`) onto a specific substrate. The **doctrine is tool-agnostic**; the adapters are where the tool-specific mechanics live.

## Two things stay separate

- **Role** — the behavioral boundary (implementer, adversarial reviewer, orchestrator, conductor). It is defined by the doctrine and by repo role prompts. It is a property of *what the agent is allowed to do*.
- **Model** — the engine a role runs on. The substrate (or a fleet registry) selects the model. Model selection and role behavior are **separate axes**: changing the model does not change the role, and the role does not dictate the model.

Keep them separate. Do not bake role text into a repo-wide instruction file, and do not let a model choice become a role choice.

## What each adapter covers

| Substrate | File | Transport specifics |
|---|---|---|
| Herdr | `herdr.md` | Multiplexer substrate + AGPL interop note |
| Claude Code | `claude-code.md` | Skill dir, prompt injection, role/enforcement placement |
| Codex | `codex.md` | Skill dir, role block at start, post-spawn prompts |
| OpenCode | `opencode.md` | Skill dir, `--prompt` at start, TUI paste race |

## Licensing note (applies to every adapter)

The doctrine is **MIT** and framework-agnostic. No adapter incorporates source from a substrate; each describes how to run the *doctrine* on that substrate. Where a substrate is **AGPL** (e.g. Herdr), the doctrine **interoperates** with it over an API — it does not derive from it, so AGPL copyleft does not apply to the doctrine or to any separate control-plane program running on top. See `herdr.md` for the full note.
