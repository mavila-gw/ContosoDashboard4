---
description: "Technical refinement of the backlog before generating tasks — feasibility analysis, risk assessment, and design challenge"
agent: "starterkit.refine"
---

Run the `starterkit.refine` agent.


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

> Invoke via the `starterkit.refine` agent for full functionality.
