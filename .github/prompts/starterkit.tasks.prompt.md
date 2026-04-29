---
description: "Expand the approved backlog into a detailed tasks.md with numbered tasks, REQ-* references, and test subtasks — ready for implementation"
agent: "starterkit.tasks"
---

Run the `starterkit.tasks` agent.


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


> Invoke via the `starterkit.tasks` agent for full functionality.
