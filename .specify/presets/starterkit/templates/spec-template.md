# Feature Spec – [Feature Name]

## Problem

[Describe the problem or need this feature addresses. Who needs it (role/persona)? What happens today without it?]

## Goals / Non-Goals

* **Goal:** [Primary goal v1 must achieve]
* **Goal:** [Additional goal]
* **Non-Goal (v1):** [What is explicitly out of scope for v1]

## Constraints

* **Client:** [UI platform / framework; accessibility target e.g. WCAG 2.1 AA]
* **Services:** [API style and auth mechanism; versioning policy if contracts change]
* **Performance:** [p95 latency targets — API and client]
* **Security:** [Auth model; authorization scope; PII handling; no PII in URLs]
* **Rollout:** Feature flag `feature.[name]`

## User Acceptance

* [Observable criterion 1 — what the user can do when the feature ships]
* [Observable criterion 2]
* [Observable criterion 3]

## APIs (Conceptual)

* `[METHOD] /api/[resource]` — [purpose]
* `[METHOD] /api/[resource]/{id}` — [purpose]

> Final request/response models are defined in code and documented in the API spec (e.g. OpenAPI); version if contracts change.

## Observability

* Events: `[event_name]`, `[event_name]`
* Metrics: [key metric 1], [key metric 2]
* Logs: include [non-PII field], [non-PII field] (no PII)
