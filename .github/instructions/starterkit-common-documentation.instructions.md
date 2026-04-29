---
description: "Documentation conventions for code, APIs, and SDD artifacts (stack-agnostic)"
applyTo:
  - "**/*.md"
  - "**/README*"
  - "**/openapi*.yaml"
  - "**/openapi*.json"
---


# Documentation Conventions

## READMEs

- Use sentence case for headings.
- Include a **Quick Start** section that gets someone running in five minutes.
- Code examples should be complete and runnable.

## Code comments

- Follow the **documentation style of each language** in this repo (e.g. XML doc comments for C#, JSDoc/TSDoc for TypeScript, docstrings for Python) when the project already does so.
- Document **why**, not **what**, in inline comments.
- Do not comment obvious code.

## API documentation

- Use **OpenAPI 3.x** (or your team's standard) for HTTP APIs; keep it aligned with live behavior and implementation models.
- Include example requests and responses.
- Document **error** responses and status codes, not only success cases.

## Specs and SDD

- Feature specs and **`REQ-…`** traceability live under `specs/`; link from PRs when required (see `sdd-principles` rule).
- Keep `specs/{slug}.spec.md` in sync with implemented behavior; update the spec when contracts change.
