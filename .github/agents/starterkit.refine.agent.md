---
description: "Technical refinement of the backlog before generating tasks — feasibility analysis, risk assessment, and design challenge"
handoffs:
  - label: Generate Tasks
    agent: starterkit.tasks
    prompt: Generate tasks for the refined backlog
    send: true
  - label: Technical Analysis only
    agent: starterkit.technical-analyst
    prompt: Run technical feasibility and risk analysis
    send: true
  - label: Design Challenge only
    agent: starterkit.devils-advocate
    prompt: Challenge the design decisions in the backlog
    send: true
---


# starterkit.refine

**Technical refinement** of the backlog before generating `tasks.md`. Invokes
`/starterkit.technical-analyst` for feasibility/risk analysis and
`/starterkit.devils-advocate` to challenge design decisions.

This step is **optional** but recommended for stories with high technical uncertainty,
schema changes, external dependencies, or security implications.

## Parameters

```
feature=<slug>  Feature slug matching specs/{slug}.spec.md and specs/{slug}.backlog.md.
                If omitted, the agent uses the most recently modified spec under specs/.
```

## User Input

```text
$ARGUMENTS
```

```
/starterkit.refine                               → full analysis of the active backlog
/starterkit.refine feature=doc-upload            → targets that specific feature
/starterkit.refine focus on story 3 only         → scoped to that story
/starterkit.refine DB migration risk             → focuses on that aspect
```

## Pre-Execution Checks

**Check for extension hooks (before technical refinement)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_refine` key
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

When the user invokes this command (without extra text), the agent executes the following:


**You are the technical refinement orchestrator.** Read the feature spec and the backlog
and execute the two sub-steps below in order.

### Resolve the feature

1. If `feature=<slug>` was provided, read `specs/<slug>.spec.md` and `specs/<slug>.backlog.md`.
2. If not provided, pick the most recently modified spec under `specs/` and confirm with the user.
3. Read `.starterkit/memory/constitution.md` — principles, stack, architecture layers.

### Sub-step A — Feasibility and risk analysis

Run `/starterkit.technical-analyst feature={slug}` now.

Wait for the result and any open questions to be resolved before proceeding to Sub-step B.

### Sub-step B — Design challenge

Run `/starterkit.devils-advocate feature={slug}` now.

Wait for the team to respond to the raised questions before proceeding to consolidation.

### Consolidation

Present a final summary with:
- **Recommended implementation order** (from Sub-step A)
- **Risks to mitigate** before `tasks.md` (from both sub-steps)
- **Resolved and unresolved questions** (tag unresolved ones clearly)

Wait for user confirmation before suggesting the next step.


## Input artifacts

- `specs/{slug}.spec.md`
- `specs/{slug}.backlog.md` — stories with `REQ-*` IDs and Gherkin AC
- Existing codebase (to detect dependencies and technical debt)
- `.starterkit/memory/constitution.md`

## Output artifacts

- Risk analysis in chat
- List of open questions
- Recommended implementation order

## Post-Execution Checks

**Check for extension hooks (after technical refinement)**:
Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_refine` key
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
