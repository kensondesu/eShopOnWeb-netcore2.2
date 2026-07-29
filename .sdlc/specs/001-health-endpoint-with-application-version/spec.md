# Feature specification: Health endpoint with application version

**Feature number:** 001
**Branch:** `feature/001-health-version`
**Requested by:** kensondesu
**Status:** Draft

> Written for the business user who asked for this, not for the implementer. Describe **what** and
> **why**; the plan describes **how**.

## Problem

The application already exposes a `/health` endpoint that reports the health status of internal
subsystems (home page reachability, API reachability). However, that endpoint does not include the
application version in its response. Without the version, operators and deployment tooling cannot
confirm which build is actually running in a given environment after a deployment completes. This
slows down deployment verification, makes rollback decisions harder, and increases time-to-resolve
when triaging production incidents.

## User scenarios

### Primary

**Given** the Web application is running
**When** any HTTP client sends a GET request to `/health`
**Then** the response has status code 200 OK and the JSON body includes a `version` field
containing the application's assembly version (e.g. `"1.0.0.0"`)

### Edge cases

- What happens when the health checks report an unhealthy subsystem?
  The response still includes the `version` field regardless of the health status of individual
  checks. The HTTP status code follows the existing behaviour (returns the appropriate status for
  the overall health state), but the version is always present in the body.
- What happens when the application is starting up but not fully ready?
  The `/health` endpoint is registered in the middleware pipeline before MVC routing. If the
  application process is alive and the middleware pipeline is running, the endpoint responds. The
  version is always available because it is read from the assembly metadata, not from an external
  dependency.

## Requirements

Each requirement is atomic, testable, and independently verifiable. An implementation either
satisfies it or does not.

| ID | Requirement | Priority |
| --- | --- | --- |
| FR-001 | The `/health` endpoint response body MUST include a `version` field containing the Web application's informational version string. | Must |
| FR-002 | The `version` value MUST reflect the actual assembly version of the running application, not a hardcoded constant. | Must |
| FR-003 | The `version` field MUST be present in the response regardless of whether individual health checks pass or fail. | Must |
| FR-004 | The endpoint MUST continue to return the existing `status` and `errors` fields alongside the new `version` field (no breaking change to the current response shape). | Must |
| FR-005 | The endpoint MUST NOT require authentication or authorisation to access. | Must |
| FR-006 | The response content type MUST remain `application/json`. | Must |

## Success criteria

Measurable outcomes that prove the problem is solved. Not implementation milestones — no
"the endpoint is written", instead "a request returns in under 200ms at p95".

| ID | Criterion | How it is measured |
| --- | --- | --- |
| SC-001 | A GET request to `/health` returns a JSON body containing a non-empty `version` field whose value matches the deployed assembly version. | Automated test asserts the field is present and non-empty. |
| SC-002 | The response time of `/health` does not regress by more than 10 ms at p95 compared to the current baseline. | Measured by existing health check response time; adding a field read from assembly metadata should add negligible latency. |
| SC-003 | Existing consumers of the `/health` endpoint continue to function without modification (no fields removed or renamed). | Automated test asserts `status` and `errors` fields are still present. |

## Out of scope

Explicitly listed so the agent does not expand the change.

- Adding separate readiness or liveness probe endpoints (e.g. `/health/ready`, `/health/live`).
- Reporting the version of downstream dependencies or infrastructure components.
- Changing the existing health check logic or adding new health checks.
- Exposing version information anywhere other than the `/health` endpoint (e.g. response headers, a dedicated `/version` endpoint).
- Changing the HTTP status code behaviour of the health endpoint.

## Clarifications

No ambiguities require clarification from the requester. The request is straightforward: add the
application version to the existing `/health` response.

| # | Category | Question | Answer |
| --- | --- | --- | --- |
| — | — | — | — |

Assumptions made without an answer:

- The "application version" refers to the assembly informational version of the Web project, which
  is the standard .NET mechanism for reporting build versions. If a `Version` or
  `InformationalVersion` property is not explicitly set in the project file, the runtime default
  (`1.0.0.0`) will be returned. This is acceptable — it will show the correct version once the
  project sets one.
- The existing response shape (`{ "status": "...", "errors": [...] }`) gains a sibling `version`
  field at the top level. This is an additive, non-breaking change.

## Dependencies and assumptions

- The existing `/health` endpoint middleware and its response-writer delegate are the integration
  point. No new middleware or NuGet packages are required.
- The assembly version is available at runtime without external dependencies.