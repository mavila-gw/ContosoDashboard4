---
description: "Generate initiative documentation for Confluence from SDD artifacts and user input."
agent: "starterkit.confluence-automation"
---

Run the `starterkit.confluence-automation` agent.


# starterkit.confluence-automation

Generates initiative documentation in Markdown, ready for Confluence or other executive/engineering review, from SDD artifacts and user input.

## Default prompt

# Confluence Initiative Automation

Generate initiative documentation in Markdown, ready for Confluence or other executive/engineering review, from SDD artifacts and user input. This core is stack-agnostic and agent-agnostic.

## Purpose

Transform a major epic or initiative (refined with `starterkit.backlog`) into initiative documentation that mirrors the structure of your organization's Confluence template. The output is suitable for direct publishing or manual paste into Confluence, and is always traceable to REQ-* IDs and stories.

## Structure

1. **Initiative metadata table** — Program, Category, Tier, Staffing, Status, Marketing, Parent ticket, INIT, Epics, UX, Test Plan, Security Compliance, QA/launch dates, Portfolio Phase, Description, Notes, Responsible, Launch Type, Legal/Training/Export, etc.
2. **Market Owner** — Problem, What/Why/Why it matters (success metrics, benefits, differentiators), Caveats.
3. **Product Owner** — Requirements, NFRs, Demo environment, Risk, UX artifacts, Happy path, Use cases, Feature interaction matrix, Questions, Not doing.

> Invoke via the `starterkit.confluence-automation` agent for full functionality.
