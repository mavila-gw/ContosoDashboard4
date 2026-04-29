---
description: "Capture a Feature Spec following Spec-Driven Design (SDD) — entry point of the development cycle"
---


# starterkit.specify

Captures the **Feature Spec** for a new capability following Spec-Driven Design (SDD).
This is the entry point of the cycle — no spec means no backlog, no tasks, no implementation.

## Parameters

```
feature=<slug>    Name of the feature (used to name specs/{slug}.spec.md).
                  If omitted, the agent asks the user for a name before proceeding.
```

## User Input

```text
$ARGUMENTS
```

If the user provides extra text with the command, that text **extends** the default prompt
as the initial problem statement (the agent skips questions already answered by that text).

```
/starterkit.specify                                    → runs the full default prompt
/starterkit.specify feature=doc-upload                 → sets the slug; runs default prompt
/starterkit.specify I want a document upload feature   → uses that as the problem statement
/starterkit.specify feature=doc-upload I want a ...   → both
```

## Pre-Execution Checks

**Check for extension hooks (before spec creation)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_specify` key
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

**You are the Feature Spec facilitator for SDD.** Your goal is to capture product intent
with enough detail for `/starterkit.backlog` to generate traceable stories.
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

Ask or infer (from `.starterkit/memory/constitution.md`):
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
These are not Gherkin yet — `/starterkit.backlog` handles that.

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


## Input artifacts

- Initial description from the user (optional)
- `.starterkit/memory/constitution.md` — principles, stack, security

## Output artifacts

- `specs/{slug}.spec.md`

## Next step

Once the spec is saved, optionally resolve open questions before generating the backlog:
```
/starterkit.clarify              ← (optional) identify and close underspecified areas in the spec
/starterkit.backlog feature=<slug>  ← generate SVS + stories + Gherkin AC
```

## Post-Execution Checks

**Check for extension hooks (after spec creation)**:
Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_specify` key
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
## Next steps

/starterkit.clarify    ← Clarify Spec Requirements
/starterkit.backlog    ← Generate Backlog
