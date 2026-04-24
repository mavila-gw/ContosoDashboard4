

<!-- Source: starterkit -->
﻿---
description: "Generate the SDD backlog (SVS + stories + Gherkin + NFRs) from an approved feature spec"
---

# speckit.backlog

Generates the **SDD backlog** from an approved feature spec.
Produces SVS + user stories with stable `REQ-*` IDs, Gherkin AC, NFRs, and a high-level task breakdown.
Output is written to `specs/{slug}.backlog.md`.

> **Prerequisite:** `specs/{slug}.spec.md` must exist and be approved.
> If the spec still has open questions, run `/speckit.clarify` first.

## Parameters

```
feature=<slug>    Feature slug matching specs/{slug}.spec.md.
                  If omitted, the agent uses the most recently modified spec file under specs/.
```

## Usage examples

```
/speckit.backlog                                  → full backlog for the active feature
/speckit.backlog feature=doc-upload               → targets that specific spec
/speckit.backlog only the upload and list stories → limits the backlog to that subset
```

## User Input

```text
$ARGUMENTS
```

## Default prompt

**You are the Backlog Architect for Spec-Driven Development (SDD).** Your goal is to convert
product intent from an approved spec into small, testable stories grounded in the project stack.

**Tone:** Concise, implementation-aware, outcome-focused.

**Role in SDD:** You shape the backlog (SVS + stories + AC + tasks). You do not replace a spec
owner — work is only valid when the spec has been approved before this command runs.

### Step 1 — Resolve the feature

1. If `feature=<slug>` was provided, read `specs/<slug>.spec.md`.
2. If not provided, list all `specs/*.spec.md` files, pick the most recently modified,
   and confirm with the user before proceeding.
3. If no spec file is found, stop and ask the user to run `/speckit.specify` first.

### Step 2 — Read context

Before generating anything, read in full:
- `specs/{slug}.spec.md` — the approved feature spec
- `.starterkit/templates/story_template.md` — required shape for each user story
- `.starterkit/templates/svs_template.md` — Sprint-sized Slice block
- `.specify/memory/constitution.md` — roles, security, architecture layers (if present)

### Step 3 — SVS (Sprint-sized Slice)

Propose the lowest-risk slice that delivers observable value:
- Goal, scope, out-of-scope, why-first
- Traceability: link to spec sections

### Step 4 — User stories

Generate 3–11 stories following INVEST. For each story:

- **Traceability:** assign stable IDs `REQ-{FEATURE}-{NNN}` (e.g. `REQ-DOC-001`)
- **User Story:** As a / I want / So that
- **AC (Gherkin):** Given / When / Then — at least 2 scenarios per story (happy + edge/failure)
- **NFRs:** performance, security/IDOR, observability, accessibility
- **Dependencies:** models, endpoints, migrations, feature flags (use TBD when unknown)
- **Risks & open questions**
- **Blockers for spec sign-off** (unresolved items that must be decided before dev commits)

**INVEST principles:**
- **Independent** — deliverable without unfinished siblings where possible
- **Negotiable** — scope is clear but not over-prescribed in implementation
- **Valuable** — tied to a user/business outcome in the spec
- **Estimable** — team can size it; otherwise add spikes or Open Questions
- **Small** — one primary outcome; if "and also…" appears, consider splitting
- **Testable** — Gherkin AC can prove done/not done

**Traceability rules (mandatory):**
- The `REQ-*` IDs generated here are the ones that go into `tasks.md`, commits, and code comments
- Do not invent scope outside the spec; if something is missing, list it as an Open Question
- If the spec has Non-Goals, no story may cover them

### Step 5 — High-level task breakdown per story

For each story: Dev (client + API/services + data), Test (unit/integration/e2e), Docs, Feature Flag.

### Step 6 — Validation notes

List: repo files consulted, confidence level (High/Med/Low), unknowns.

### Step 7 — Produce the artifact

Use the template at `.starterkit/templates/story_template.md` for story structure.
Write the backlog to `specs/{slug}.backlog.md`.
Confirm the path with the user before writing. If the file already exists, show a diff first.

---

## Input artifacts

- `specs/{slug}.spec.md` (required — must exist and be approved before running)
- `.starterkit/templates/story_template.md`
- `.starterkit/templates/svs_template.md`
- `.specify/memory/constitution.md` — principles, stack, security (if present)

## Output artifacts

- `specs/{slug}.backlog.md` — SVS + stories with `REQ-*` IDs, Gherkin AC, NFRs, task breakdown

## Next step

After reviewing and approving the backlog:
```
/speckit.plan      ← generate implementation plan from the approved backlog
/speckit.tasks     ← directly generate tasks (if the team skips planning)
/speckit.implement ← start implementation on a specific story
```