---
description: "Generate initiative documentation for Confluence from SDD artifacts and user input."
---


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
4. **Engineering** — Proposed solution, Assumptions, Estimates by team.

Use standard Markdown tables and headings. Do not attempt to reproduce Confluence-specific placeholders or UI elements.

## Inputs

- Epic/initiative description (problem, audience, constraints)
- SDD artifacts: SVS, stories, REQ-* traceability, Gherkin AC, NFRs, dependencies, risks, task breakdown (from `starterkit.backlog`)
- Target Confluence page ID (optional, for automation)
- Cloud site (optional, for automation)

## Outputs

1. Full initiative document in Markdown, structured per `starterkit/templates/initiative_confluence_skeleton.md`.
2. Mapping note: which REQ/story filled which section (traceability).
3. If page ID is provided: automation-ready payload for Confluence API (see your organization's publishing workflow).

## Guardrails

- Security/Compliance: Only link to real compliance pages if provided; otherwise use "TBD" and list open questions.
- No secrets in page body.
- Preserve REQ-* IDs throughout for SDD traceability.
- Reference `.starterkit/memory/constitution.md` for stack-specific patterns, compliance, or security requirements.
