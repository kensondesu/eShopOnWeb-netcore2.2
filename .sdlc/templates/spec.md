# Feature specification: [FEATURE NAME]

**Feature number:** NNN
**Branch:** `feature/NNN-slug`
**Requested by:** [UPN]
**Status:** Draft | Clarifying | Ready | Implemented

> Written for the business user who asked for this, not for the implementer. Describe **what** and
> **why**; the plan describes **how**. If you cannot write a sentence without naming a class, a
> table or a library, it belongs in the plan.

## Problem

What is wrong today, for whom, and what does it cost them? One paragraph.

## User scenarios

### Primary

**Given** [starting state]
**When** [the user does this]
**Then** [this observable thing happens]

### Edge cases

- What happens when [boundary condition]?
- What happens when [the dependency is unavailable]?
- What happens when [the user lacks permission]?

## Requirements

Each requirement is atomic, testable, and independently verifiable. An implementation either
satisfies it or does not.

| ID | Requirement | Priority |
| --- | --- | --- |
| FR-001 | The system MUST … | Must |
| FR-002 | The system MUST … | Must |
| FR-003 | The system SHOULD … | Should |

## Success criteria

Measurable outcomes that prove the problem is solved. Not implementation milestones — no
"the endpoint is written", instead "a request returns in under 200ms at p95".

| ID | Criterion | How it is measured |
| --- | --- | --- |
| SC-001 | … | … |
| SC-002 | … | … |

## Out of scope

Explicitly listed so the agent does not expand the change. Scope creep is the single most common
way an agent run goes wrong.

- …

## Clarifications

Ambiguities resolved with the requester before implementation began. At most five questions are
asked; anything still unresolved is recorded here as an assumption with its risk.

| # | Category | Question | Answer |
| --- | --- | --- | --- |
| 1 | | | |

Assumptions made without an answer:

- …

## Dependencies and assumptions

- …
