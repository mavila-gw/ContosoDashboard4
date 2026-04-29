---
description: "Capture a Feature Spec following Spec-Driven Design (SDD) — entry point of the development cycle"
agent: "starterkit.specify"
---

Run the `starterkit.specify` agent.


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

> Invoke via the `starterkit.specify` agent for full functionality.
