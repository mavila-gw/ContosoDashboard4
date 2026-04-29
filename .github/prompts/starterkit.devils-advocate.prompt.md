---
description: "Challenge design decisions in the backlog with adversarial questioning — surface hidden risks, edge cases, and architectural weaknesses"
agent: "starterkit.devils-advocate"
---

Run the `starterkit.devils-advocate` agent.


# starterkit.devils-advocate

**Challenges design decisions** in the feature spec and backlog through adversarial
questioning. Surfaces hidden risks, unconsidered edge cases, and potential architectural
weaknesses — without proposing solutions.

Typically invoked by `/starterkit.refine` after `/starterkit.technical-analyst`, but can be
run standalone.

## Parameters

```
feature=<slug>  Feature slug matching specs/{slug}.spec.md and specs/{slug}.backlog.md.
                If omitted, the agent uses the most recently modified spec under specs/.
```

## User Input

```text

> Invoke via the `starterkit.devils-advocate` agent for full functionality.
