---
description: "Implement one story at a time from tasks.md — code, tests, and traceable commit per story"
agent: "starterkit.implement"
---

Run the `starterkit.implement` agent.


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

> Invoke via the `starterkit.implement` agent for full functionality.
