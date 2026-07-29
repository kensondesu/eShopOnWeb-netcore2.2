# Implementation plan: [FEATURE NAME]

**Spec:** `./spec.md`
**Feature number:** NNN

> The **how**. Written by the orchestrator model after the spec is Ready, reviewed against
> `.sdlc/constitution.md`.

## Approach

Two or three paragraphs describing the shape of the change and *why this approach over the
alternatives*. If there was no real alternative, say so.

## Constitution check

| Rule | How this plan complies |
| --- | --- |
| Every behavioural change ships with a test | … |
| Smallest change that fully solves the problem | … |
| No new dependency without justification | … |

Any deviation must be justified here explicitly, or the plan is rejected.

## Affected surfaces

| Path | Change | Why |
| --- | --- | --- |
| `src/…` | modify | … |
| `tests/…` | add | … |

## Data and contract changes

Schema migrations, API contract changes, and their backward-compatibility story. **Every migration
must be reversible**, because Dev environments are torn down and recreated per branch and
production promotion requires a rollback path.

## Test strategy

| Requirement | Test | Level |
| --- | --- | --- |
| FR-001 | … | unit / integration / e2e |

Each `FR-NNN` maps to at least one test that would fail without the change.

## Deployment and verification

How the Dev deployment is proven healthy, and what the QA reviewer should look at when the
approval card arrives.

## Risks

| Risk | Likelihood | Mitigation |
| --- | --- | --- |
| … | | |
