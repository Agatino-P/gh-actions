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

## New-machine setup — what to install (e.g. when on the Mac)
When I'm on a fresh machine (the Mac), install this exact set so the repo is runnable —
nothing here syncs via git, it's all per-machine:
- **Docker Desktop** — the runner backend; must be running for `act`.
- **`act`** (nektos/act) — runs the workflows locally.
- **`gh`** — GitHub CLI.
- **`jq`** — JSON tooling.
- VS Code extension **"GitHub Local Actions"**.
- Recreate **`~/.actrc`** pinning the medium image (see Toolchain section for the exact lines).

macOS (Homebrew):
```bash
brew install --cask docker          # Docker Desktop (then launch it once)
brew install act gh jq
printf '%s\n' \
  '-P ubuntu-latest=catthehacker/ubuntu:act-latest' \
  '-P ubuntu-24.04=catthehacker/ubuntu:act-latest' \
  '-P ubuntu-22.04=catthehacker/ubuntu:act-latest' \
  '-P ubuntu-20.04=catthehacker/ubuntu:act-latest' > ~/.actrc
```
Windows (winget), for reference — already done on the original PC:
`winget install nektos.act GitHub.cli jqlang.jq` + Docker Desktop + `~/.actrc`.

## State as of 2026-06-14 (Mac, verified in a fresh shell)
- ✅ Windows PC: `act` v0.2.89, `gh` v2.94.0, `jq` v1.8.1 (winget), Docker Desktop v29.5.3 (WSL2), `~/.actrc`
- ✅ **Mac**: `act` v0.2.89 (brew), `gh` v2.93.0 (brew), `jq` 1.7.1 (Apple system build at `/usr/bin/jq`),
  Docker Desktop v29.5.3 (daemon running), `~/.actrc` recreated. `code` CLI IS on PATH (`/usr/local/bin/code`).
  VS Code **"GitHub Local Actions" (`sanjulaganepola.github-local-actions`) v1.2.5 installed** on the Mac.
  (Also present: GitHub's official `github.vscode-github-actions` v0.32.0 — that one is YAML editing + viewing
  runs on GitHub.com, NOT local `act` execution; don't confuse the two.)
- ✅ **First workflow written & runs green locally**: `.github/workflows/hello.yml` — push-triggered `hello`
  job, ran via `act` on the Mac (success).
- ⚠️ **Mac-only `~/.actrc` line**: appended `--container-architecture linux/amd64` so plain `act` works on
  Apple Silicon without passing the flag each time. This line is **Mac-specific** — do NOT copy it to the
  Windows PC's `~/.actrc` (Windows runs amd64 natively).
- ✅ **Debugging basics covered (teaching session 2026-06-14):** `act -l` (list/parse), `act -n` (dryrun),
  real run, and reading a deliberate failure. Key facts learned: steps run under `bash -e` (any non-zero
  command aborts the step); `|`-prefixed log lines = output from inside the container; `act` derives the
  `github.*` context from local sources (e.g. `github.repository` from the `origin` git remote), so values
  can drift from real GitHub; `act` exits non-zero on job failure. `act -v` = verbose firehose, grep it for
  a specific mystery value rather than reading top-to-bottom.
- ✅ **Shell-diving / container lifecycle learned:** `act` default = remove container on **success**, KEEP it on
  **failure** (a failed run leaves a debuggable container behind). `-r/--reuse` = keep on success too AND reuse
  it next run (state persists — handy but can mask bugs, so do a final clean run without `-r`). `--rm` = remove
  even on failure. Inspect with `docker exec -it <id|name> bash`. Cleanup leftovers:
  `docker ps -aq --filter "name=act-" | xargs docker rm -f`.
- ✅ **"GitHub Local Actions" extension evaluated:** it's a **GUI wrapper around the same `act` engine** — it
  literally builds an `act ...` command and shows it. Real wins: COMPONENTS health-check (act + Docker green),
  one-click run from a WORKFLOWS tree, persisted run HISTORY (status + per-step timing tree; logs saved to
  `~/Library/Application Support/Code/User/globalStorage/sanjulaganepola.github-local-actions/*.log`), and
  SETTINGS param forms (Secrets/Variables/Inputs/Payloads/Runners/Options) that auto-detect references and feed
  values in (secrets passed as `--secret NAME`, value out-of-band via env so it's not in the visible command).
  It silently honors `~/.actrc` too. Limits: NOT a different engine; clicking a HISTORY step does NOT navigate
  the log (flat scroll). Verdict: not essential, but history + set-params-once are nice.

## Next step (resume here)
Hello workflow + full `act` debugging toolkit done (list/dryrun/run/read-failure/exit-codes/verbose/shell-dive
+ container lifecycle), and the "GitHub Local Actions" VS Code extension evaluated. Practice steps
(`Break on purpose`, `Use a secret`) were added to learn and then reverted — `hello.yml` is back to green.
Options to continue learning:
- `continue-on-error` / `if:` conditions to control what a failure does (started but not yet practiced).
- Add more triggers (`workflow_dispatch`, `pull_request`, `schedule`) to a workflow.
- Try a matrix build, job dependencies (`needs:`), or a marketplace action (e.g. `actions/checkout`).
- Pass inputs/secrets to `act` (`-s`, `--input`, `--var`, event payload via `-e event.json`).

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
- **Apple Silicon (Mac):** `act` warns about missing container architecture and amd64-only images can
  misbehave — needs `--container-architecture linux/amd64`. On this Mac that flag now lives in `~/.actrc`,
  so plain `act` already includes it (no need to pass it per-command). New Macs: add that line to `~/.actrc`.
- **zsh word-splitting:** unlike bash, zsh does NOT split an unquoted `$var` on spaces — `act -l $dr` sends
  the whole string as one arg (`unknown flag: --container-architecture linux/amd64`). Use `${=dr}` to force
  splitting, or an array `dr=(--container-architecture linux/amd64)`. Better: put recurring flags in `~/.actrc`.
