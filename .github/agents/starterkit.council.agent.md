---
description: "Spawn parallel exploration agents for broad codebase orientation or focused investigation"
handoffs:
  - label: Build Plan
    agent: starterkit.plan
    prompt: Build a plan based on the council findings
  - label: Create Spec
    agent: starterkit.specify
    prompt: Create a spec for the feature we explored
---


# starterkit.council

Use this command when you need **broad codebase exploration** before answering, planning,
or debugging — spawns parallel agents to investigate different areas simultaneously.

## Parameters

```
n=<number>      Number of parallel exploration agents to spawn (default: 10).
feature=<slug>  Scope exploration to a specific feature's artifacts under specs/.
```

## User Input

```text
$ARGUMENTS
```

Any text provided alongside the command becomes the **area of interest** for exploration.
The default prompt (broad orientation) is replaced by that specific question or goal.

```
/starterkit.council                                         → broad orientation of the codebase
/starterkit.council n=15 how does authentication work?      → 15 agents on that topic
/starterkit.council n=5 getting this error, investigate     → focused debugging
/starterkit.council feature=doc-upload map the data flow    → scoped to that feature
/starterkit.council map all service interfaces and their callers
/starterkit.council show every place we validate file size
```

## Pre-Execution Checks

**Check for extension hooks (before council exploration)**:
- Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_council` key
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

# council

Use this command when you need **broad codebase exploration** before answering or planning.

## Stack context

See `.starterkit/memory/constitution.md` for the project-specific stack (frontend, backend, data layers, infra).
When reporting findings, tie discoveries to the architecture layers defined there and, if relevant, to **traceability** (spec sections, requirement IDs).

## Steps

Based on the given area of interest:

1. Dig around the codebase for that area; gather keywords and an **architecture overview** (layer boundaries, shared contracts, integration points).
2. Spawn **n = 10** task agents to dig deeper (unless the user specifies another **n**), with **variety** in exploration paths (e.g. UI, API, data, tests, infra).
3. Use the collected information to do what the user requested.
4. If in **plan mode**, use the information to build the plan **in line with SDD** (scope, risks, tasks mapped to specs).

## Example usage

```
/starterkit.council n=15 how does authentication work?
/starterkit.council map all ViewModels and their navigation targets
/starterkit.council n=5 getting this error, investigate
/starterkit.council Map out the data flow from login to dashboard
/starterkit.council Show me every place we touch the document store
```


## Input artifacts

- Entire codebase (or scoped area of interest)
- `.starterkit/memory/constitution.md`
- `specs/{slug}.*` (if `feature=<slug>` provided)

## Output

- Architecture overview with layer boundaries and shared contracts
- Findings per exploration area
- Risks, gaps, and SDD traceability notes
- Suggested next steps or follow-up deep dives

## Post-Execution Checks

**Check for extension hooks (after council exploration)**:
Check if `.starterkit/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.after_council` key
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
