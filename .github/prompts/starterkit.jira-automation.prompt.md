---
description: "Structure Jira work as Epics, Stories, Tasks, and Sub-tasks with SDD traceability (REQ-*)"
agent: "starterkit.jira-automation"
---

Run the `starterkit.jira-automation` agent.


# starterkit.jira-automation

Structures Jira work items (Epics, Stories, Tasks, Sub-tasks) with consistent naming, parent links, and SDD traceability (`REQ-…`). Use when creating or breaking down Jira work from a backlog, importing structured YAML/JSON, automating Epic→Story→Task hierarchies, or when the user mentions epics, stories, tasks, sub-tasks, or manager tracking.

## Parameters

```
feature=<slug>    Read stories from specs/<slug>.backlog.md as source.
                  If omitted, the user provides stories or YAML directly.
```

## User Input

```text
$ARGUMENTS
```

## Default prompt


> Invoke via the `starterkit.jira-automation` agent for full functionality.
