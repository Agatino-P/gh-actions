# gh-actions

A practice repository for learning **GitHub Actions** — locally with [`act`](https://github.com/nektos/act) and on GitHub.

## Local tooling

- **act** `v0.2.89` — run workflows locally (pinned to the medium runner image via `~/.actrc`)
- **GitHub Local Actions** VS Code extension — run/manage workflows from the editor
- **Docker Desktop** — provides the runner containers

## Running a workflow locally

```bash
# list workflows/jobs act can see
act -l

# run the default (push) event
act

# run a specific job
act -j <job-id>
```

## Layout

```
.github/workflows/   # workflow YAML files live here
```
