# Eshoponweb Netcore2.2 — agent instructions

This repository runs an **agentic SDLC**. Read this before making any change.

## How work arrives here

A business user requests a feature in Microsoft Teams. The orchestrator then, in this order:

1. Creates the branch `feature/NNN-slug` off `main`.
2. Commits `.sdlc/specs/NNN-slug/spec.md` to it.
3. Opens the issue.
4. Opens a **draft pull request** whose body starts `Closes #N`.

The branch is created **before** the issue, deliberately, so the issue is bound to a branch that
already exists. The draft pull request is the binding artifact — it is what makes the branch show
up in the issue's Development panel. Do not try to re-link them by other means.

The branch name uses the **feature number**, not the issue number, because it must be computable
before the issue exists.

## Your working agreement

`.sdlc/constitution.md` is binding. Read it. The reviewer model checks your diff against it and
will send work back.

The essentials:

- Work only on your own `feature/NNN-*` branch.
- **Never** touch `.github/workflows/**`, `CODEOWNERS`, or `.sdlc/constitution.md`. A repository
  ruleset blocks this at push time as well, so an attempt fails the run.
- Every behavioural change ships with a test.
- Build, lint and tests must be green before the pull request leaves draft.
- Smallest change that fully solves the problem. Do not refactor adjacent code or reformat files
  you did not otherwise need to touch.

## Where things live

| Path | What |
| --- | --- |
| `.sdlc/config.yml` | Resolved configuration — models, delivery target, telemetry, budgets |
| `.sdlc/constitution.md` | The binding rules for every change |
| `.sdlc/specs/NNN-slug/` | `spec.md` (what and why), `plan.md` (how), `tasks.md` (ordered work) |
| `.sdlc/templates/` | The blank spec, plan and tasks templates |
| `.github/workflows/sdlc-*.yml` | Intake, implement, deploy and promotion pipelines |

Work `tasks.md` top to bottom and check items off as you commit.

## Commit trailers

Every commit you make carries:

```
Co-authored-by: GitHub Copilot <copilot@github.com>
X-SDLC-Requester: <upn>
X-SDLC-Session: <copilot session id>
```

These are what tie a line of code back to the person who asked for it and the conversation that
produced it.

## Environments

| Environment | Gate |
| --- | --- |
| `dev` | None — deployed automatically for every feature branch |
| `qa` | GitHub Required Reviewers |
| `production` | GitHub Required Reviewers, self-review prevented, admin bypass off |


Dev runs on localhost via docker compose behind a dev tunnel — there is no cloud dev environment.


Production promotion re-uses the exact image digest built once for Dev. Never rebuild for
production — that is a different artifact from the one that was approved.
