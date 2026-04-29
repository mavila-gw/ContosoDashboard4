---
description: "Implement one story at a time from tasks.md — code, tests, and traceable commit per story"
---


# starterkit.implement

Implements **one story at a time** from `tasks.md`: code + tests + traceable commit.
Waits for user confirmation before moving to the next story.

## Parameters

```
feature=<slug>  Feature slug. If omitted, uses the most recently modified spec under specs/.
story=N         Story number to implement (required unless story=all).
story=all       Iterates all pending (⬜) stories, waiting for confirmation between each.
e2e=defer       Defers E2E tests with documented justification in tasks.md.
```

## User Input

```text
$ARGUMENTS
```

```
/starterkit.implement feature=doc-upload story=3
/starterkit.implement feature=doc-upload story=3 e2e=defer
/starterkit.implement feature=doc-upload story=3 use the repository pattern instead of direct DbContext
/starterkit.implement feature=doc-upload story=all
```

## Pre-Execution Checks

**Check for extension hooks (before implementation)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_implement` key.
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

**You are the SDD implementation agent.** Implement the indicated story strictly following
all available spec artifacts and the conventions in the constitution.

### Step 1 — Resolve context

**Feature resolution:**
1. If `feature=<slug>` is provided, use that slug.
2. If not provided, list all `specs/*.spec.md` files, pick the most recently modified,
   and confirm with the user before proceeding.

**Read all available spec artifacts for the resolved feature:**

| Artifact | Path | Required |
|---|---|---|
| Feature spec | `specs/{slug}.spec.md` | Yes |
| Backlog (stories + REQ-* + Gherkin AC) | `specs/{slug}.backlog.md` | Yes |
| Tasks | `specs/{slug}.tasks.md` | Yes |
| Plan / refinement output | `specs/{slug}.plan.md` | If exists |
| Constitution | `.starterkit/memory/constitution.md` | Yes |

Locate story N in `tasks.md` with all its tasks (N.1, N.2 …).
Read the Gherkin AC for that story from `backlog.md`.
Verify task statuses — only implement tasks marked `⬜`.

### Step 2 — Implementation order within the story

Always follow this order (adapt to the active stack):

1. Models / entities / schema (migrations if applicable)
2. DTOs / API contracts
3. Service layer (interfaces first, then implementation)
4. Controllers / endpoints / pages
5. UI / components / navigation
6. Unit tests (≥ 60 % of the story's total tests)
7. Integration tests (≥ 20 %)
8. E2E tests (≥ 20 %, or documented deferral)

### Step 3 — Mandatory conventions

- **Traceability:** class or method header with `REQ-XXX-NNN — task N.M — JIRA-KEY`.
- **Stack conventions:** follow the stack and architecture defined in `.starterkit/memory/constitution.md`.
- **No scope expansion:** if a task is not in `tasks.md`, do not implement it without confirmation.

### Step 4 — Tests

- **Unit tests:** isolate with mocks (Moq or equivalent); cover happy path + at least
  one negative/edge case per critical method.
- **Integration tests:** use in-memory or real DB + real storage; do not mock the entire stack.
- **E2E tests:** Playwright/Cypress or equivalent against the real server. If `e2e=defer`,
  create the test file with described cases marked `[Skip]` / `skip` with the comment
  `// E2E deferred: <justification>`.

### Step 5 — Mark tasks complete

After completing each task, mark it as `✅` in `specs/{slug}.tasks.md` immediately.
Do not batch-update at the end.

### Step 6 — Commit

After completing **all** items in the story (code + tests), make **one single commit**:

```
feat(REQ-XXX-NNN): JIRA-KEY Story N – <story title>

Tasks: N.1, N.2, N.3[, ...]
Tests: unit (X), integration (Y)[, e2e (Z) | e2e: deferred – <reason>]

Refs: REQ-XXX-NNN, JIRA-KEY
```

**Commit rules:**
- One commit per story — do not mix multiple stories in one commit.
- Conventional type: `feat` for new functionality, `fix` for bug fix,
  `refactor` for behavior-neutral refactoring.
- Do not bypass hooks (`--no-verify`) unless the user explicitly authorizes it.

### Step 7 — Confirm and continue

After the commit, show a summary:

```
Story N complete
  Tasks:  N.1 ✅  N.2 ✅  N.3 ✅
  Tests:  unit (X)  integration (Y)  e2e (Z / deferred)
  Commit: <sha>

Continue with story N+1? (y/n)
```

Wait for user confirmation before the next story.

## Post-Execution Checks

**Check for extension hooks (after implementation)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_implement` key.
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

| Artifact | Path |
|---|---|
| Feature spec | `specs/{slug}.spec.md` |
| Backlog | `specs/{slug}.backlog.md` |
| Tasks | `specs/{slug}.tasks.md` |
| Plan / refinement | `specs/{slug}.plan.md` (if exists) |
| Constitution | `.starterkit/memory/constitution.md` |

## Output artifacts

- Source code (models, services, UI, controllers)
- Tests (unit, integration, E2E or skeletons marked as deferred)
- Traceable commit per story
## Next steps

/starterkit.analyze    ← Analyze For Consistency
