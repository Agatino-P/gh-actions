# CLAUDE.md — project context (synced via git for cross-PC work)

This file travels with the repo so any machine that clones/pulls picks up where we left off.
(Private per-machine memory lives in `~/.claude/...` and is NOT synced — keep durable context here.)

## Instructions for Claude (this repo only)
- **Keep this file current.** Whenever work meaningfully changes the project state — a tool
  installed, a workflow added, the "Next step" reached — update the relevant section below
  (especially **State** and **Next step**) in the same session, before wrapping up.
- **Keep it pushed.** After updating `CLAUDE.md`, commit and `git push origin main` so other
  PCs stay in sync via `pull`. Don't leave context changes sitting uncommitted.
- Record only durable, cross-PC-relevant context here; leave machine-local or throwaway
  details out (those belong in private `~/.claude` memory).

## What we're doing
Practicing / learning **GitHub Actions**. Plan: author workflows under `.github/workflows/` and
run them **locally with [`act`](https://github.com/nektos/act)** (and the "GitHub Local Actions"
VS Code extension) before/instead of pushing. Docker Desktop provides the runner containers.

## Toolchain (intended)
- `act` pinned to the **medium** runner image via `~/.actrc`
  (`ubuntu-latest` / `ubuntu-24.04` / `-22.04` / `-20.04` → `catthehacker/ubuntu:act-latest`).
  Note: `~/.actrc` is a per-machine home-dir file — recreate it on each new PC.
- "GitHub Local Actions" VS Code extension.
- Docker Desktop (WSL2) as the runner backend — must be running for `act`.

## State as of 2026-06-13 (verified in a fresh shell)
- ✅ `act` v0.2.89, `gh` v2.94.0, `jq` v1.8.1 installed (winget)
- ✅ Docker Desktop v29.5.3 (WSL2), Git installed, GitHub connected
- ✅ `~/.actrc` staged on the original PC
- ❌ **No `.github/workflows/` yet** — zero workflows written. This is the frontier.

## Next step (resume here)
1. Create the first workflow in `.github/workflows/` (e.g. simple push-triggered hello job).
2. Run locally: `act -l` to list, then `act` (default push event) or `act -j <job-id>`.
   Docker Desktop must be running.

## Running a workflow locally
```bash
act -l            # list workflows/jobs act can see
act               # run the default (push) event
act -j <job-id>   # run a specific job
```

## Gotchas
- After winget installs, PATH isn't live in already-open shells — open a fresh terminal
  (or use the profile `refreshenv` / `Reload-Env` helper). The Bash tool runs a separate
  shell from PowerShell, so a freshly installed Windows tool can show "command not found"
  in Bash while being on the Windows PATH — verify with PowerShell `Get-Command <tool>`.
- On a new PC, the `act`/`gh`/`jq` installs and `~/.actrc` won't exist until you set them up.
