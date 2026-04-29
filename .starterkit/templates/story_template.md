# Story Template

> **Used by:** `starterkit.backlog` — fill each user story using this structure.  
> **Filled example:** `templates/examples/example_story.md`.
> **Other stacks:** see `constitution.md` for the active stack; list **Open Questions** for unknowns instead of force-fitting.

---

**Title:** \<clear, concise\>

---

## Traceability

| Field | Value |
|-------|--------|
| Requirement ID | `REQ-…` |
| Spec / PRD | §… or path to `spec.md` / doc |

---

## User Story

As a \<role>, I want \<capability>, so that \<outcome>.

---

## INVEST check

Short note: **I**ndependent / **N**egotiable / **V**aluable / **E**stimable / **S**mall / **T**estable — call out any concern or split suggestion.

---

## Acceptance Criteria (Gherkin)

- Given \<precondition\>
  When \<action\>
  Then \<result\>
- Given …

---

## Non-Functional Requirements

- **Performance:** bundle size if UI-heavy; p95 latency; pagination/caching strategy
- **Security / Privacy:** auth model, PII, audit, CSRF if cookies
- **Reliability:** retries/timeouts; idempotency for writes
- **Observability:** events; service logs/metrics/traces
- **Accessibility:** keyboard, focus, ARIA, contrast (WCAG 2.1 AA target)

---

## Dependencies

> List stack-specific dependencies per `constitution.md`. Examples: frontend modules/services/routes; backend endpoints/models/migrations; feature flags/config.

- **Feature flags / config:** …

---

## Risks & Mitigations

- …

---

## Open Questions

- …

---

## Blockers for spec sign-off

**None** — or list must-resolve items (legal, security architecture, data/UX contract) before dev commits.

---

## Suggested Task Breakdown

- **Dev:** component/service + API endpoint + wiring/flags
- **Test:** unit, integration, e2e; **contract tests** if API is shared/consumed elsewhere
- **Docs:** user help, API (OpenAPI), ADR
- **Data / Migration:** design, schema migration, backfill
- **Feature Flag:** rollout plan + metrics

---

## Validation Notes (optional)

Use for handoff to `tasks.md` or epic rollup.

- **Repo references:** files/dirs/PRs (or TBD)
- **Confidence:** High / Med / Low
- **Traceability summary:** story title ↔ `REQ-…`
