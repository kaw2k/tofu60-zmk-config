---
name: deploy-firmware
description: Use when user asks to commit or push BT60 ZMK changes, deploy firmware, watch GitHub Actions Build, wait for artifact, or download bt60.uf2 to Downloads.
---

# Deploy BT60 firmware

Commit only requested source/configuration changes. Push current branch. Watch GitHub Actions `Build`. Download `firmware` artifact only after successful run.

## Preconditions

- Read `AGENTS.md` and inspect `git status --short`.
- User must explicitly request commit/push/deploy/download. Ask if missing.
- Preserve unrelated local changes. Never stage `.DS_Store`, `firmware-artifact/`, or generated `.uf2`.
- Confirm `gh auth status` succeeds.

## Procedure

1. Review intended diff. Stage explicit paths only:
   ```bash
   git add config/bt60.keymap
   ```
2. Commit with requested or conventional message. Push current branch:
   ```bash
   git commit -m "feat: adjust BT60 keymap"
   git push origin "$(git branch --show-current)"
   ```
3. Resolve Build run for pushed commit, not merely newest run:
   ```bash
   sha="$(git rev-parse HEAD)"
   run_id="$(gh run list --workflow Build --commit "$sha" --json databaseId --jq '.[0].databaseId')"
   test -n "$run_id"
   ```
   If absent, wait briefly and retry. Do not select another commit's run.
4. Wait and fail on build failure:
   ```bash
   gh run watch "$run_id" --exit-status
   ```
   On failure, report run URL and failed logs. Do not download artifact.
5. Download into unique folder under Downloads, never project tree:
   ```bash
   short_sha="$(git rev-parse --short HEAD)"
   target="$HOME/Downloads/bt60-firmware-$short_sha"
   test ! -e "$target"
   mkdir -p "$target"
   gh run download "$run_id" --name firmware --dir "$target"
   test -f "$target/bt60.uf2"
   ```
6. Report absolute `bt60.uf2` path and run URL:
   ```bash
   gh run view "$run_id" --json url --jq .url
   ```

## Artifact collision

If target directory exists, do not overwrite or merge. Ask user whether to choose a new directory or replace existing output.

## Success checks

- Commit points only at requested files.
- Pushed `HEAD` SHA matches watched run SHA.
- Build conclusion is `success`.
- `$target/bt60.uf2` exists.
