---
description: "Expand the approved backlog into a detailed tasks.md with numbered tasks, REQ-* references, and test subtasks — ready for implementation"
handoffs:
  - label: Implement Project
    agent: starterkit.implement
    prompt: Start the implementation in phases
    send: true
  - label: Analyze For Consistency
    agent: starterkit.analyze
    prompt: Run a project analysis for consistency
    send: true
---


# starterkit.tasks

Expands the approved backlog into a **detailed `specs/{slug}.tasks.md`**: numbered tasks
(N.M), `REQ-*` references, Jira keys, and test subtasks — ready for implementation.

## Parameters

```
feature=<slug>  Feature slug matching specs/{slug}.spec.md and specs/{slug}.backlog.md.
                If omitted, the agent uses the most recently modified spec under specs/.
story=N         (Optional) Generate tasks only for story N.
```

## User Input

```text
$ARGUMENTS
```

```
/starterkit.tasks                              → generates the full tasks.md for the active feature
/starterkit.tasks feature=doc-upload           → targets that specific feature
/starterkit.tasks feature=doc-upload story=3   → only story 3
/starterkit.tasks include migration tasks      → adds that extra block
```

## Pre-Execution Checks

**Check for extension hooks (before tasks generation)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_tasks` key.
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally.
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable.
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation.
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

    Wait for the result of the hook command before proceeding to the Outline.
    ```
- If no hooks are registered or `.starterkit/extensions.yml` does not exist, skip silently.

## Outline

**You are the agent that generates `specs/{slug}.tasks.md`.** Read the feature spec,
backlog, and refinement output (if available) and produce the task file with enough
detail that implementation requires no additional interpretation.

### Step 1 — Resolve the feature

1. If `feature=<slug>` was provided, read `specs/<slug>.spec.md` and `specs/<slug>.backlog.md`.
2. If not provided, pick the most recently modified spec under `specs/` and confirm with the user.
3. Check whether `specs/<slug>.plan.md` exists (refinement output); if so, use its recommended
   implementation order and risk notes.
4. Read `.starterkit/memory/constitution.md` — principles, stack, architecture layers.

### Step 2 — Generate tasks

Use this structure for `specs/{slug}.tasks.md`:

```markdown
# Tasks — {Feature Name}

> Spec:    specs/{slug}.spec.md
> Backlog: specs/{slug}.backlog.md
> Plan:    specs/{slug}.plan.md  (if exists)
> Stack:   {active stack}

## Story summary

| # | Story | REQ-* | Jira | Estimate | Status |
|---|---|---|---|---|---|
| 1 | Title | REQ-XXX-001 | JIRA-KEY | S | ⬜ |


### Story N — Title · [JIRA-KEY] · REQ-XXX-NNN

> AC: [link to the story or literal Gherkin quote]

| Task | Technical description | Jira sub-task | Status |
|---|---|---|---|
| N.1 | Concrete technical description | JIRA-KEY | ⬜ |
| N.2 | ... | ... | ⬜ |

#### Required tests (Story N)

| ID | Type | Description | Status |
|---|---|---|---|
| N.T1 | Unit | ... | ⬜ |
| N.T2 | Integration | ... | ⬜ |
| N.T3 | E2E | ... | ⬜ |


## Summary table (all stories)

| Story | Dev tasks | Unit tests | Integration tests | E2E tests | Jira Epic | Jira sub-tasks |
```

### Step 3 — Apply generation rules

1. Each task (N.M) must be **atomic**: one file, one method, one migration, one view.
   Do not group multiple concerns into a single task.
2. `REQ-*` IDs must match **exactly** those in `specs/{slug}.backlog.md` — do not invent new ones.
3. Tests: target ratio **60 % unit / 20 % integration / 20 % E2E**.
   If E2E is deferred, mark with `⏸ E2E deferred: <justification>` in the test table.
4. Initial status for all tasks: `⬜`.
5. If Jira keys are available from prior tooling, include them; otherwise use `TBD` as placeholder.
6. After generating, ask the user whether to adjust order or detail level
   before confirming the file.

### Step 4 — Write the file

Write `specs/{slug}.tasks.md` only after user confirmation.
If the file already exists, show a diff summary before overwriting.

## Post-Execution Checks

**Check for extension hooks (after tasks generation)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_tasks` key.
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally.
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable.
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation.
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
- If no hooks are registered or `.starterkit/extensions.yml` does not exist, skip silently.

## Input artifacts

- `specs/{slug}.spec.md` (required)
- `specs/{slug}.backlog.md` — stories with `REQ-*` IDs (required)
- `specs/{slug}.plan.md` — refinement output with ordering and risks (if available)
- `.starterkit/memory/constitution.md`

## Output artifacts

- `specs/{slug}.tasks.md`
