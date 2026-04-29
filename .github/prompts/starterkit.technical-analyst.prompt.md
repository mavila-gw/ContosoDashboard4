---
description: "Assess technical feasibility and risk for each backlog story — complexity, dependencies, security, estimates, and optimal implementation order"
agent: "starterkit.technical-analyst"
---

Run the `starterkit.technical-analyst` agent.


# starterkit.technical-analyst

Performs a **technical feasibility and risk assessment** of the backlog before task
generation. Produces a recommended implementation order and surfaces open questions
that must be resolved before writing `tasks.md`.

Typically invoked by `/starterkit.refine`, but can be run standalone.

## Parameters

```
feature=<slug>  Feature slug matching specs/{slug}.spec.md and specs/{slug}.backlog.md.
                If omitted, the agent uses the most recently modified spec under specs/.
```

## User Input

```text
$ARGUMENTS

> Invoke via the `starterkit.technical-analyst` agent for full functionality.
