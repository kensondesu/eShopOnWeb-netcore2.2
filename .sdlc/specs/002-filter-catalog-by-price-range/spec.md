# Feature specification: Filter catalog by price range

**Feature number:** 001
**Branch:** `feature/001-price-range-filter`
**Requested by:** kensondesu
**Status:** Draft

> Written for the business user who asked for this, not for the implementer. Describe **what** and
> **why**; the plan describes **how**. If you cannot write a sentence without naming a class, a
> table or a library, it belongs in the plan.

## Problem

Today a shopper browsing the catalog can narrow results by brand and by product type, but there is
no way to filter by price. A shopper looking for items within their budget must scroll through
every page, mentally discarding products that are too expensive or too cheap. For a catalog that
grows over time this wastes effort and makes the shop harder to use, especially on mobile where
paging is slower.

## User scenarios

### Primary

**Given** a shopper is on the catalog page and items span a range of prices (e.g. $8.50 – $19.50
in the seed catalog)
**When** the shopper enters a minimum price of $10 and a maximum price of $15 and submits the
filter
**Then** only items whose price is between $10 and $15 inclusive are shown, pagination reflects the
reduced count, and the selected price range is preserved in the filter controls

### Combining with existing filters

**Given** a shopper has selected a brand, a type, **and** a price range
**When** the filter is submitted
**Then** only items that match **all three** criteria are returned

### Only minimum price supplied

**Given** a shopper enters a minimum price of $10 but leaves the maximum price empty
**When** the filter is submitted
**Then** all items priced at $10 or above are shown, regardless of how high the price goes

### Only maximum price supplied

**Given** a shopper enters a maximum price of $12 but leaves the minimum price empty
**When** the filter is submitted
**Then** all items priced at $12 or below are shown

### No filters applied

**Given** a shopper leaves both the minimum and maximum price fields empty
**When** the filter is submitted
**Then** the price range has no effect and all items (subject to any brand/type filters) are shown
— the same behaviour as today

### Edge cases

- What happens when no items match the selected price range? The catalog already displays
  "THERE ARE NO RESULTS THAT MATCH YOUR SEARCH". The same message is shown.
- What happens when the shopper enters a minimum price greater than the maximum? The system
  treats this as an empty result set and shows the no-results message. The filter values are
  preserved so the shopper can correct the mistake.
- What happens when the shopper enters a negative price or zero? The filter treats any value
  below zero as if it were not supplied (ignored). Zero is a valid minimum price.
- What happens when the shopper enters non-numeric text? The input fields accept only numeric
  values. Non-numeric input is rejected by the browser before submission.

## Requirements

Each requirement is atomic, testable, and independently verifiable. An implementation either
satisfies it or does not.

| ID | Requirement | Priority |
| --- | --- | --- |
| FR-001 | The catalog page MUST allow the shopper to specify an optional minimum price. When supplied, only items with a price greater than or equal to this value are returned. | Must |
| FR-002 | The catalog page MUST allow the shopper to specify an optional maximum price. When supplied, only items with a price less than or equal to this value are returned. | Must |
| FR-003 | Minimum and maximum price filters MUST compose with the existing brand and type filters using AND logic — an item must satisfy all active filters to appear. | Must |
| FR-004 | When a price range filter is active the total item count and pagination MUST reflect only the filtered results. | Must |
| FR-005 | The selected minimum and maximum price values MUST be preserved in the filter controls after the page reloads with results, so the shopper can see what filter is active. | Must |
| FR-006 | Both price fields are optional. When neither is supplied the behaviour MUST be identical to the current behaviour (no price filtering). | Must |
| FR-007 | The API endpoint that serves catalog items MUST also accept optional minimum and maximum price parameters with the same filtering behaviour. | Must |
| FR-008 | The price filter inputs SHOULD appear on the same filter bar as the existing brand and type filters, between the type dropdown and the submit button. | Should |
| FR-009 | Negative price values SHOULD be ignored (treated as if not supplied). Zero is a valid minimum price. | Should |

## Success criteria

Measurable outcomes that prove the problem is solved. Not implementation milestones — no
"the endpoint is written", instead "a request returns in under 200ms at p95".

| ID | Criterion | How it is measured |
| --- | --- | --- |
| SC-001 | Filtering the seed catalog (12 items) by price range $10–$15 returns exactly the items whose price falls within that range, and no others. | Automated test against the seed data. |
| SC-002 | Combining a price range filter with a brand or type filter returns only items that match all criteria. | Automated test. |
| SC-003 | Supplying only a minimum or only a maximum price returns the correct open-ended subset. | Automated test. |
| SC-004 | Pagination item count and page count are correct when a price range filter reduces the result set. | Automated test. |
| SC-005 | Page load time with the price filter active does not regress — p95 response time remains under 500 ms for the seed catalog. | Manual or load-test measurement on the dev environment. |

## Out of scope

Explicitly listed so the agent does not expand the change. Scope creep is the single most common
way an agent run goes wrong.

- Predefined price "buckets" or a slider control — the filter uses two simple numeric input fields.
- Sorting by price (ascending/descending) — this is a separate feature.
- Displaying the price range of available products (e.g. "Prices range from $8.50 to $19.50").
- Currency conversion or multi-currency support.
- Any change to the basket, checkout, or order flows.
- Changes to the admin or back-office experience.

## Clarifications

Ambiguities resolved with the requester before implementation began. At most five questions are
asked; anything still unresolved is recorded here as an assumption with its risk.

| # | Category | Question | Answer |
| --- | --- | --- | --- |
| 1 | User experience | Should the price filter use free-text numeric inputs or a slider/range control? | [NEEDS CLARIFICATION: Free-text numeric inputs are assumed because they are simpler and consistent with the existing dropdown pattern. If a slider is preferred, the scope and effort increase.] |

Assumptions made without an answer:

- The price filter uses two simple numeric input fields (minimum and maximum), not a slider or predefined buckets. Risk: low — a slider is a UI-only change that can be layered on later.
- The currency is whatever is already stored in the catalog (a single decimal price per item). No currency symbol or locale-specific formatting is introduced beyond what already exists.
- The price values in the filter are compared against the stored item price as-is, with no rounding or tolerance. This matches how existing brand/type filters work (exact match against stored values).

## Dependencies and assumptions

- The catalog item entity already has a decimal `Price` field — no schema change is needed.
- The existing specification pattern (used for brand and type filtering) supports adding additional filter criteria without architectural changes.
- The caching layer that wraps catalog queries will need its cache key extended to include the new price parameters, but this is an implementation detail that does not change the external contract.