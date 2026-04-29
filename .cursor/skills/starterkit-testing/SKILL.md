---
description: "Developer testing mindset: generate use-case and risk-based test ideas, unit test plans, and dev test checklists."
---


# starterkit.testing

Helps developers strengthen tests while writing code by thinking like QA: use cases, failure modes, boundaries—not just literal acceptance criteria.

## Default prompt

# Developer Testing Mindset

Help developers strengthen tests while writing code by thinking like QA: use cases, failure modes, boundaries—not just literal acceptance criteria. This core is stack-agnostic and agent-agnostic.

## Role in SDD

- Consumes: Story/spec slice (see `starterkit/templates/story_template.md`), REQ-*, Gherkin AC, current change intent, NFRs.
- Produces: Use-case and risk-based test ideas; unit-test focus; integration hooks for boundaries; mapping to AC and REQ-* when available; DoD (dev test) checklist.

## Inputs

- What you are changing: file paths, service/component names, API route/method, or diff summary.
- Traceability: REQ-*, story title, Jira key (if any).
- Story + AC: Paste or point to story section (optional for pure unit guidance).
- Stack: See `.starterkit/memory/constitution.md` for test stack/tooling.

## Outputs

1. Traceability — REQ/story/Jira or TBD
2. Use cases under test — short bullets, including beyond AC where code implies extra behavior
3. Unit test plan — grouped by module/class/function/component: suggested test names, what to mock, key assertions
4. Integration/e2e hooks — only where unit tests are insufficient
5. AC alignment (when AC exists) — table: AC or Gherkin line → test case; note which AC lines have no unit coverage by design
6. Edge/negative list — concise bullets tied to the current change
7. Fixtures & test data — factories, tenants, auth headers, minimal payloads
8. Definition of Done (dev testing) — checkboxes a dev can tick before opening PR
9. Gaps & questions — unclear AC, untestable UI, need spike

- Reference `.starterkit/memory/constitution.md` for stack-specific test/linter/CI tools.
