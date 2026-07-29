# Feature specification: Basket item count in header

**Feature number:** 001
**Branch:** `feature/001-basket-header-count`
**Requested by:** kensondesu
**Status:** Draft

> Written for the business user who asked for this, not for the implementer. Describe **what** and
> **why**; the plan describes **how**. If you cannot write a sentence without naming a class, a
> table or a library, it belongs in the plan.

## Problem

Shoppers browsing the catalogue cannot tell at a glance how many items are in their basket without
navigating to the basket page. The header currently shows a small numeric badge next to the cart
icon, but it always displays — even when the basket is empty — and provides no textual context
about what the number represents. This forces shoppers to click through to the basket page to
confirm what they have added, interrupting their browsing flow and reducing confidence that an
"Add to basket" action succeeded.

## User scenarios

### Primary

**Given** a shopper has added one or more items to their basket
**When** they navigate to any page in the store
**Then** the header displays the total number of items (sum of all quantities) alongside the basket
icon, clearly readable without hovering or clicking

### Add item feedback

**Given** a shopper is on the catalogue page with 2 items already in the basket
**When** they add another item
**Then** on the next page load the header count updates from 2 to 3

### Empty basket

**Given** a shopper has not added any items (or has removed all items)
**When** they view any page
**Then** the basket count indicator is either hidden or displays in a way that clearly
communicates "empty" (e.g. no badge, or "0" in a muted style)

### Edge cases

- What happens when the shopper is not signed in? The count reflects the anonymous basket tied to their browser session.
- What happens when the basket data cannot be retrieved (e.g. database unavailable)? The header still renders; the count defaults to 0 or is hidden, and the page does not error.
- What happens when the shopper signs in and had items in an anonymous basket? The count reflects whichever basket state the sign-in process resolves to (existing behaviour; this feature does not change basket-merge logic).

## Requirements

Each requirement is atomic, testable, and independently verifiable. An implementation either
satisfies it or does not.

| ID | Requirement | Priority |
| --- | --- | --- |
| FR-001 | The site header MUST display the total quantity of items in the shopper's basket (sum of individual item quantities, not distinct product count). | Must |
| FR-002 | The basket count MUST be visible on every page of the site that uses the shared layout, for both authenticated and anonymous shoppers. | Must |
| FR-003 | When the basket is empty (zero items), the count badge MUST be visually distinct from a non-empty state — either hidden entirely or displayed in a clearly muted/empty style. | Must |
| FR-004 | The count MUST update to reflect the current basket state on each full page load (server-rendered accuracy). | Must |
| FR-005 | The basket count SHOULD be accessible — screen readers should announce it as contextual information (e.g. "Basket: 3 items"). | Should |
| FR-006 | The basket count area MUST link to the basket page so shoppers can click through to review their items. | Must |

## Success criteria

Measurable outcomes that prove the problem is solved. Not implementation milestones — no
"the endpoint is written", instead "a request returns in under 200ms at p95".

| ID | Criterion | How it is measured |
| --- | --- | --- |
| SC-001 | On every page using the shared layout, the header displays the correct basket item total matching the sum of quantities in the shopper's basket. | Automated test: add known quantities, assert header output equals expected sum. |
| SC-002 | When the basket is empty, the badge is visually differentiated (hidden or muted) — no misleading "0" displayed identically to a non-empty count. | Visual inspection and automated test asserting the empty-state markup differs from the populated-state markup. |
| SC-003 | The header basket count renders without adding more than 50 ms to page response time at p95. | Load test or performance assertion in test suite. |

## Out of scope

Explicitly listed so the agent does not expand the change. Scope creep is the single most common
way an agent run goes wrong.

- Real-time (JavaScript/WebSocket) count updates without a page reload.
- Basket item count in email notifications or external channels.
- Changing basket-merge behaviour on sign-in.
- Redesigning the header layout beyond the basket count indicator.
- Mini-basket dropdown/flyout showing individual items on hover.

## Clarifications

Ambiguities resolved with the requester before implementation began. At most five questions are
asked; anything still unresolved is recorded here as an assumption with its risk.

| # | Category | Question | Answer |
| --- | --- | --- | --- |
| 1 | User experience | Should the badge be hidden entirely when empty, or show "0" in a muted style? | [NEEDS CLARIFICATION: Hidden badge vs muted zero — either satisfies FR-003 but the visual impact differs.] |

Assumptions made without an answer:

- The count represents total quantity (e.g. 3 units of the same product counts as 3), not distinct line items. This matches current basket behaviour.
- Server-side rendering on each page load is sufficient; no live push updates are required.
- The existing basket icon and link-to-basket-page interaction pattern is acceptable; only the count visibility needs improvement.

## Dependencies and assumptions

- The site already has a shared layout rendered on every page, and a basket component that retrieves item counts server-side. This feature builds on that infrastructure.
- Anonymous baskets are identified by a browser cookie; this feature does not introduce a new persistence mechanism.
- No external service dependencies are introduced; the basket data source is the existing application database.