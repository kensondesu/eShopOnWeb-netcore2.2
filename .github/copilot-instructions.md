# Copilot instructions — Eshoponweb Netcore2.2

This repository runs an agentic SDLC. **`AGENTS.md` in the repository root is the authoritative
brief**; this file exists because GitHub Copilot loads it automatically in editors and on
github.com, where `AGENTS.md` may not be read.

Read `AGENTS.md` and `.sdlc/constitution.md` before making any change.

## The short version

- Work only on a `feature/NNN-slug` branch. The branch is created **before** its issue,
  and a draft pull request whose body starts `Closes #N` is what binds them.
- Never edit `.github/workflows/**`, `CODEOWNERS`, or `.sdlc/constitution.md`. Those govern what an
  agent may do and who approves it, so a change to them cannot be self-approved. A ruleset blocks
  it at push time and the run's permission policy blocks it before that.
- The specification for the work is `.sdlc/specs/NNN-slug/spec.md`. The plan is `plan.md` next to
  it. Specifications describe *what* and *why*; plans describe *how*.
- Every behavioural change ships with a test that would fail without it.
- Run `dotnet test` before you commit.

## Conventions

Follow the conventions already present in the surrounding code over any general preference. A
change that is stylistically inconsistent with its neighbours costs the reviewer more than it
saved you.

Make the smallest change that fully solves the problem. Do not refactor adjacent code, and do not
reformat files you did not otherwise need to touch — an unrelated reformat buries the real diff.

## Models

| Role | Model |
| --- | --- |
| Orchestrator (specs, plans) | `claude-opus-4.6` |
| Implementer (code) | `claude-sonnet-4.6` |
| Reviewer (diff critique) | `claude-sonnet-4.6` |

Change these in `.sdlc/config.yml`, or re-run `copier update --trust`.
