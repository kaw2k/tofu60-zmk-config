# Agent context maintenance

Applies to: `AGENTS.md`, `.agents/**`, `.claude/**`, `.codex/**`, `.pi/**`, `.github/workflows/**`, `config/**`.

## Rule

`AGENTS.md` is canonical repo context. Keep it accurate enough for next agent to identify board, config entry points, layers, build workflow, artifact name, deployment flow, and generated-file policy.

Before changing a keymap, workflow, agent wiring, or deployment skill:

1. Read `AGENTS.md`.
2. Read affected source/configuration.
3. Preserve unrelated local changes.

Update `AGENTS.md` in same change when any listed fact, command, path, layer architecture, workflow trigger, artifact name, board, or guardrail changes.

## Required

- Put portable agent rules in `.agents/rules/`.
- Put reusable repo procedures in `.agents/skills/<name>/SKILL.md`.
- Keep `CLAUDE.md` symlinked to `AGENTS.md`.
- Keep `.claude/skills` and `.codex/skills` linked to `.agents/skills`.
- Keep `.pi/settings.json` loading `.agents/skills`.
- Keep adapter files pointers only. Never duplicate canonical rule text.

## Forbidden

- Guessing firmware behavior from outdated comments.
- Staging `firmware-artifact/`, `.uf2`, `.DS_Store`, or user files without explicit request.
- Committing, pushing, deployment, or Downloads overwrite without explicit user request.
