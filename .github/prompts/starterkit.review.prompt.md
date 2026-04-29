---
description: "Orchestrate post-implementation QA review of a story, set of stories, or changeset against spec artifacts and acceptance criteria"
agent: "starterkit.review"
---

Run the `starterkit.review` agent.


# starterkit.review

Orchestrates **post-implementation QA review** of a story, set of stories, or changeset.
Reviews implemented code against spec artifacts, Gherkin AC, and REQ-* traceability.

## Parameters

```
feature=<slug>        Feature slug. If omitted, uses the most recently modified spec under specs/.
story=N               Review story N.
story=N,M             Review stories N and M.
jira=KEY              Review work associated with that Jira key.
changeset=<sha>       Review changes introduced in that commit or range (sha1..sha2).
sprint=<name>         Review all ✅ stories in the named sprint.
```

At least one scope parameter (`story`, `jira`, `changeset`, or `sprint`) is **required**.

## User Input

> Invoke via the `starterkit.review` agent for full functionality.
