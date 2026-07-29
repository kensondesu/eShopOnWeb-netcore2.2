# Engineering constitution — Eshoponweb Netcore2.2

Non-negotiable rules for every change an agent makes in this repository. The orchestrator loads
this file into the system prompt for every run, and the reviewer model checks the diff against it
before a pull request is marked ready for review.

Edit this file to encode your team's standards. It is the highest-leverage file in the template:
it is the difference between an agent that produces plausible code and one that produces code you
would merge.

<!-- copier:skip-if-exists — your edits here are never overwritten by `copier update`. -->

## Scope

An agent run may only:

- Create and modify files on its own `feature/NNN-*` branch.
- Write to application source, tests, and its own `.sdlc/specs/NNN-*/` directory.

An agent run may **never**:

- Modify `.github/workflows/**`, `CODEOWNERS`, `.sdlc/constitution.md`, or any ruleset. These are
  enforced by a repository ruleset as well, so a violation fails the push, not just the review.
- Commit secrets, tokens, private keys, or `.env` files with real values.
- Force-push, rewrite history, or delete branches other than its own.
- Change the base branch (`main`) directly.

## Quality bar

1. **Every behavioural change ships with a test.** A change with no test is incomplete, not
   "tested manually".
2. **The build, lint and test suite must pass** before the pull request leaves draft. A red run is
   not handed to a human to fix.
3. **No new warnings.** Warnings are how a codebase rots.
4. **Prefer the smallest change that fully solves the problem.** Do not refactor adjacent code,
   reformat untouched files, or "improve" things outside the spec's scope.
5. **Do not add a dependency without justifying it in the pull request body.** Prefer the standard
   library and what is already in the manifest.
6. **Match existing patterns.** Read neighbouring code first; consistency beats personal
   preference.

## Traceability

Every commit an agent makes carries:

```
Co-authored-by: GitHub Copilot <copilot@github.com>
X-SDLC-Requester: <upn of the business user who asked>
X-SDLC-Session: <Copilot session id>
```

Every pull request body opens with `Closes #N`, which is what binds the branch to its issue.

## Security

- Validate and sanitise all external input at the boundary.
- Never interpolate user input into a shell command, SQL query, or template.
- Secrets come from the environment or a secret store, never from source.
- New endpoints are authenticated by default; making one public is an explicit, justified decision
  recorded in the spec.

## Definition of done

A feature is done when:

- Every `FR-NNN` in the spec has an implementation and a test that would fail without it.
- Every `SC-NNN` success criterion is measurable and demonstrated.
- The Dev deployment is healthy.
- The pull request describes what changed and why, not just what the diff shows.
