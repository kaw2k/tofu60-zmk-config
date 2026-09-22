# tofu60-zmk-config

ZMK firmware config for Polarity Works BT60 / Tofu60 keyboard. GitHub Actions builds `bt60.uf2` from `config/` and publishes artifact `firmware`.

Canonical agent instructions. Edit this file and `.agents/rules/`; do not edit harness adapters. `CLAUDE.md` links here.

## Repo map

| path | purpose |
| --- | --- |
| `config/bt60.keymap` | ZMK layers, bindings, sensor bindings |
| `config/bt60.conf` | keyboard Kconfig |
| `config/west.yml` | pinned ZMK/Zephyr dependencies |
| `config/info.json` | physical layout metadata |
| `.github/workflows/build.yml` | build, artifact upload |
| `firmware-artifact/` | local downloaded output; untracked, never stage by default |

## Firmware facts

- Board: `bt60`.
- Workflow: `Build`; runs on push, pull request, and manual dispatch.
- Artifact: `firmware`, containing `bt60.uf2`.
- Layer names: `default_layer` (0), `raise` (1), `connectivity` (2).
- Layer access: bottom-left `&mo 2`; Space and Fn/left-arrow keys use `&lt 1`.
- `raise`: F/G use `&kp C_BRI_DN` / `&kp C_BRI_UP` for host display brightness; B/N use `&bl BL_INC` / `&bl BL_DEC` for keyboard backlight.
- `connectivity`: F/G mirror host display-brightness controls.

## Conventions by area

| touching | read |
| --- | --- |
| `AGENTS.md`, `.agents/**`, `.claude/**`, `.codex/**`, `.pi/**` | `.agents/rules/agent-context.md` |
| keymap, firmware build, artifact deployment | `.agents/skills/deploy-firmware/SKILL.md` when committing, pushing, watching Build, or downloading firmware |

## Commands

```bash
# inspect local changes
git status --short

# inspect recent Build runs
gh run list --workflow Build --limit 5

# download known run artifact
gh run download <run-id> --name firmware --dir "$HOME/Downloads/bt60-firmware-<short-sha>"
```

## Guardrails

- Preserve key positions. Each layer must keep same binding count as physical layout.
- Use ZMK keycodes and behaviors already supported by pinned `config/west.yml`; verify upstream syntax before adding unfamiliar behavior.
- Do not stage `.DS_Store`, `firmware-artifact/`, generated `.uf2`, or unrelated user changes.
- Do not commit, push, trigger deployment, or overwrite Downloads output without explicit user request.
- After changing agent instructions, rules, skills, workflow, artifact name, board, or layout/layer architecture: update this file in same change.
