---
description: "Generate the SDD backlog (SVS + stories + Gherkin + NFRs) from an approved feature spec"
agent: "starterkit.backlog"
---

Run the `starterkit.backlog` agent.


# starterkit.backlog

Generates the **SDD backlog** from an approved feature spec.
Produces SVS + user stories with stable `REQ-*` IDs, Gherkin AC, NFRs, and a high-level task breakdown.
Output is written to `specs/{slug}.backlog.md`.

> **Prerequisite:** `specs/{slug}.spec.md` must exist and be approved.
> If the spec still has open questions, run `/starterkit.clarify` first.

## Parameters

```
feature=<slug>    Feature slug matching specs/{slug}.spec.md.
                  If omitted, the agent uses the most recently modified spec file under specs/.
```

## User Input

```text

> Invoke via the `starterkit.backlog` agent for full functionality.
