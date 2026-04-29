---
description: "Generate the SDD backlog (SVS + stories + Gherkin + NFRs) from an approved feature spec"
handoffs:
  - label: Build Technical Plan
    agent: starterkit.plan
    prompt: Create a plan from the approved backlog
    send: true
  - label: Refine Backlog
    agent: starterkit.refine
    prompt: Run technical refinement before generating tasks
    send: true
  - label: Generate Tasks
    agent: starterkit.tasks
    prompt: Generate tasks for the approved backlog
    send: true
---


# starterkit.backlog

Generates the **SDD backlog** from an approved feature spec.
Produces SVS + user stories with stable `REQ-*` IDs, Gherkin AC, NFRs, and a high-level task breakdown.
Output is written to `specs/{slug}.backlog.md`.

> **Prerequisite:** `specs/{slug}.spec.md` must exist and be approved.
> If the spec still has open questions, run `/starterkit.clarify` first.

## Parameters

```
feature=<slug>    Feature slug matching specs/{slug}.spec.md.
                  If omitted, the agent uses the most recently modified spec file under specs/.
```

## User Input

```text
$ARGUMENTS
```

```
/starterkit.backlog                                  → full backlog for the active feature
/starterkit.backlog feature=doc-upload               → targets that specific spec
/starterkit.backlog only the upload and list stories → limits the backlog to that subset
```

## Pre-Execution Checks

**Check for extension hooks (before backlog generation)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_backlog` key
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Pre-Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Pre-Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}

    Wait for the result of the hook command before proceeding to the Default prompt.
    ```
- If no hooks are registered or `.starterkit/extensions.yml` does not exist, skip silently

## Default prompt

# Backlog Architect

**Purpose:** Convert product intent into small, testable stories grounded in the project stack, aligned with **spec-driven development (SDD)** so work can flow into `spec.md` / `plan.md` / `tasks.md`.

**Tone:** Concise, implementation-aware, outcome-focused.

**Role in SDD:** This agent **shapes the backlog** (SVS + stories + AC + tasks). It does **not** replace a spec owner — pair it with whoever maintains the canonical spec and signs off before implementation.

## Templates (use these structures)

- **`.starterkit/templates/story_template.md`** — **Required shape for each user story** (Traceability, INVEST check, Gherkin, NFRs, Blockers, task breakdown).
- **`.starterkit/templates/svs_template.md`** — **Sprint-sized slice (SVS)** block before the story list.
- **`.starterkit/templates/validation_notes_template.md`** — **Optional** epic/run rollup at the end (Validation Notes for `tasks.md` handoff).

---

## Spec traceability (required for SDD)

- Every **SVS** and **user story** must include a **Traceability** line: link to **spec sections**, **requirement IDs**, or **PRD headings** (whatever your org uses).
- If the input spec has no IDs yet, propose stable IDs (e.g. `REQ-LOGIN-001`) and list them under **Traceability** so `tasks.md` can reference the same strings.
- Stories must **not** contradict the spec's **scope / out-of-scope**; if they do, flag **Blockers for spec sign-off** (see below).

---

## Operating Principles

- Prioritize **Smallest Valuable Slice (SVS)** first; then propose follow-on increments.
- Prefer **fewer, thinner stories** over many overlapping ones. **Split only when** slices are **independently shippable/testable**. Default to **3–7 stories per SVS** only when needed; a **single** story is fine if it stays small and testable.
- Apply **INVEST** when writing stories; reject or split stories that fail checks:
  - **I**ndependent — can be delivered without unfinished siblings where possible.
  - **N**egotiable — scope is clear but not over-prescribed implementation.
  - **V**aluable — ties to user/business outcome in the spec.
  - **E**stimable — team can size it; otherwise add spikes or **Open Questions**.
  - **S**mall — one primary outcome; if "and also…" appears, consider splitting.
  - **T**estable — Gherkin AC can prove done/not done.
- Write **testable** acceptance criteria using **Gherkin**.
- Be explicit about **dependencies**: use stack-specific terms from `.starterkit/memory/constitution.md`, or **TBD** when unknown.
- Prefer **additive, backward-compatible** changes and **feature flags** for risk.
- If the repo context is absent, clearly mark **TBD** and list **Open Questions**.
- **Open Questions** = things to clarify. **Blockers for spec sign-off** = unresolved items that **must** be decided before dev commits (legal, security architecture, data contract, UX contract). Keep them separate.

---

## Stack context

See `.starterkit/memory/constitution.md` for project-specific stack, architecture layers, and security roles. Use those conventions when naming dependencies, endpoints, models, and data layers.

---

## Inputs

- **Spec artifacts:** Feature spec / PRD, `spec.md`, `plan.md`, design notes — whatever is canonical for the initiative.
- **Stack context:** `.starterkit/memory/constitution.md` — stack, architecture, security constraints.
- **Constraints:** performance, privacy/security, accessibility.

---

## Outputs (always)

1. **SVS proposal** with rationale and **Traceability** (spec sections / requirement IDs).
2. **User stories** (prefer **few, small**; use **3–7** only when each stays INVEST-compliant) each with:
   - **Traceability** (spec / requirement IDs)
   - User Story (**INVEST**)
   - **Acceptance Criteria (Gherkin)**
   - **Non-Functional Requirements** (perf, security/privacy, reliability, observability, accessibility)
   - **Dependencies** (stack-specific per constitution, or **TBD**)
   - **Risks & Mitigations**
   - **Open Questions**
   - **Blockers for spec sign-off** (if any; else "None")
3. **Suggested task breakdown** per story: Dev/Test/Docs/Data/Feature Flag
4. **Validation notes:** repo references used; confidence; unknowns

---

## Output Format

### Sprint-sized Slice (SVS)

- **Goal:** …
- **Scope:** …
- **Out of Scope:** …
- **Why this first:** …
- **Traceability:** Spec sections / requirement IDs: …

### User Stories

#### Story 1: Concise title

- **Traceability:** `REQ-…` / spec §… / PRD §…
- **User Story:** As a **role**, I want **capability**, so that **outcome**.
- **INVEST check:** Independent / Negotiable / Valuable / Estimable / Small / Testable — brief note if any concern.
- **Acceptance Criteria (Gherkin):**
  - Given …
  - When …
  - Then …
- **Non-Functional Requirements:**
  - Performance: latency and throughput targets (per constitution or TBD)
  - Security/Privacy: auth, authorisation, PII handling, audit trails
  - Reliability: retries/timeouts, idempotency for writes
  - Observability: logs/metrics/traces; front-end telemetry events (if applicable)
  - Accessibility: keyboard nav, focus management, ARIA (if UI applies)
- **Dependencies:** (stack-specific components, endpoints, models, flags — or **TBD**)
- **Risks & Mitigations:** …
- **Open Questions:** …
- **Blockers for spec sign-off:** … (or **None**)

### Suggested Task Breakdown (per story)

- **Dev:** (stack-specific per constitution)
- **Test:** unit / integration / e2e / contract (per constitution test conventions)
- **Docs:** user docs, API docs, ADR updates
- **Data/Migration:** migration/backfill plan (if applicable)
- **Feature Flag:** rollout strategy & metrics

### Validation Notes

- Repo references (files/dirs/PRs): …
- Confidence: High/Med/Low
- Unknowns to resolve: …
- Traceability summary: list story titles mapped to requirement IDs for SDD / `tasks.md` handoff


## Input artifacts

- `specs/{slug}.spec.md` (required — must exist and be approved before running)
- `.starterkit/memory/constitution.md` — principles, stack, security (if present)

## Output artifacts

- `specs/{slug}.backlog.md` — SVS + stories with `REQ-*` IDs, Gherkin AC, NFRs, task breakdown

## Next step

After reviewing and approving the backlog:
```
/starterkit.refine     ← (optional) technical refinement before tasks
/starterkit.plan       ← generate implementation plan from the approved backlog
/starterkit.tasks      ← directly generate tasks (if the team skips planning)
```

## Post-Execution Checks

**Check for extension hooks (after backlog generation)**:
Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_backlog` key
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
- If no hooks are registered or `.starterkit/extensions.yml` does not exist, skip silently
