# Tasks: [FEATURE NAME]

**Spec:** `./spec.md` · **Plan:** `./plan.md`

> Ordered, individually verifiable units of work. The implementer model works this list top to
> bottom and checks items off by committing. Each task names the requirement it serves, so an
> orphan task is visible immediately.

Marking: `[ ]` pending · `[~]` in progress · `[x]` done · `[!]` blocked (say why inline).

## Setup

- [ ] T001 — … · _serves:_ —

## Tests first

Written before the implementation so they fail for the right reason.

- [ ] T010 — Failing test for … · _serves:_ FR-001
- [ ] T011 — Failing test for … · _serves:_ FR-002

## Implementation

- [ ] T020 — … · _serves:_ FR-001
- [ ] T021 — … · _serves:_ FR-002

## Verification

- [ ] T090 — Full build, lint and test suite green
- [ ] T091 — Dev deployment healthy and SC-001 demonstrated
- [ ] T092 — Self-review against `.sdlc/constitution.md`, pull request taken out of draft

## Traceability

| Requirement | Tasks | Test |
| --- | --- | --- |
| FR-001 | T010, T020 | … |
| FR-002 | T011, T021 | … |
