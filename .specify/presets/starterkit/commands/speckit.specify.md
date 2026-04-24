---
description: "Capture a Feature Spec following Spec-Driven Design (SDD) — entry point of the development cycle"
---

# starterkit-spec

Captures the **Feature Spec** for a new capability following Specs-Driven Design (SDD).
This is the entry point of the cycle — no spec means no backlog, no tasks, no implementation.

## Parameters

```
feature=<slug>    Name of the feature (used to name specs/{slug}.spec.md).
                  If omitted, the agent asks the user for a name before proceeding.
```

## Prompt override

If the user provides extra text with the command, that text **extends** the default prompt
as the initial problem statement (the agent skips questions already answered by that text).

```
/speckit.specify                                    → runs the full default prompt
/speckit.specify feature=doc-upload                 → sets the slug; runs default prompt
/speckit.specify I want a document upload feature   → uses that as the problem statement
/speckit.specify feature=doc-upload I want a ...   → both
```

## User Input

```text
$ARGUMENTS
```

## Default prompt

**You are the Feature Spec facilitator for SDD.** Your goal is to capture product intent
with enough detail for `/speckit.backlog` to generate traceable stories.
Follow these steps in order and wait for the user's answer before moving on.

### Step 1 — Problem and motivation

Ask the user:
- What problem or need does this feature address?
- Who needs it (role / persona)?
- What happens today without it?

### Step 2 — Goals and Non-Goals

Using the answers from Step 1, propose a list of Goals (what v1 must achieve) and
Non-Goals (what is explicitly out of scope). Ask for confirmation or adjustments.

### Step 3 — Constraints

Ask or infer (from `.specify/memory/constitution.md` and the active stack pack):
- Performance: p95 latency targets (API, client)
- Security: auth model, IDOR, PII handling, audit trail
- Accessibility: WCAG 2.1 AA if applicable
- Rollout: feature flag (`feature.<name>`)
- Others: file size limits, rate limiting, data retention

### Step 4 — Conceptual APIs (if applicable)

If the feature exposes or consumes endpoints, sketch conceptual contracts:
- Routes, HTTP methods, main entities
- Note that exact models are defined in code and documented in OpenAPI / equivalent

### Step 5 — User acceptance criteria (high level)

List 3–7 observable criteria (what the user can do when the feature ships).
These are not Gherkin yet — `/speckit.backlog` handles that.

### Step 6 — Observability

Propose structured log events (`snake_case`), key metrics, and field names
(no PII: userId, resourceId, action, duration).

### Step 7 — Produce the artifact

Generate the file `specs/{slug}.spec.md` using the `spec-template` structure:

```markdown
# Feature Spec – <Name>

## Problem
## Goals / Non-Goals
## Constraints
## User Acceptance
## APIs (Conceptual)
## Observability
```

Confirm the path with the user before writing. If the file already exists, show a diff.

---

## Input artifacts

- Initial description from the user (optional)
- `.specify/memory/constitution.md` — principles, stack, security
- Active stack pack (if present at `.cursor/rules/stack-packs/…`)

## Output artifacts

- `specs/{slug}.spec.md`

## Next step

Once the spec is saved, optionally resolve open questions before generating the backlog:
```
/speckit.clarify              ← (optional) identify and close underspecified areas in the spec
/speckit.backlog feature=<slug>  ← generate SVS + stories + Gherkin AC
```
