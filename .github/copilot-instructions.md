# Copilot Instructions — Starterkit

This project uses the **Starterkit SDD toolkit**.

## Available Agents

Use these agents for Spec-Driven Development:

- **@starterkit.analyze** — Perform a non-destructive cross-artifact consistency and quality analysis across spec.md, plan.md, and tasks.md after task generation.
- **@starterkit.backlog** — Generate the SDD backlog (SVS + stories + Gherkin + NFRs) from an approved feature spec
- **@starterkit.checklist** — Generate a custom checklist for the current feature — unit tests for requirements quality
- **@starterkit.clarify** — Identify underspecified areas in the current feature spec by asking up to 5 highly targeted clarification questions and encoding answers back into the spec.
- **@starterkit.clean-code** — Identify and remove AI-generated slop, placeholder names, dead code, and style inconsistencies
- **@starterkit.confluence-automation** — Generate initiative documentation for Confluence from SDD artifacts and user input.
- **@starterkit.constitution** — Create or update the project constitution and deploy stack rules to the active AI agent
- **@starterkit.council** — Spawn parallel exploration agents for broad codebase orientation or focused investigation
- **@starterkit.devils-advocate** — Challenge design decisions in the backlog with adversarial questioning — surface hidden risks, edge cases, and architectural weaknesses
- **@starterkit.implement** — Implement one story at a time from tasks.md — code, tests, and traceable commit per story
- **@starterkit.jira-automation** — Structure Jira work as Epics, Stories, Tasks, and Sub-tasks with SDD traceability (REQ-*)
- **@starterkit.plan** — Execute the implementation planning workflow using the plan template to generate design artifacts.
- **@starterkit.refine** — Technical refinement of the backlog before generating tasks — feasibility analysis, risk assessment, and design challenge
- **@starterkit.review** — Orchestrate post-implementation QA review of a story, set of stories, or changeset against spec artifacts and acceptance criteria
- **@starterkit.specify** — Capture a Feature Spec following Spec-Driven Design (SDD) — entry point of the development cycle
- **@starterkit.sync-core** — Toolkit maintenance command: clean and adapt a legacy cursor file (agent, rule, or template) into a stack-agnostic, agent-agnostic equivalent under starterkit/.
- **@starterkit.tasks** — Expand the approved backlog into a detailed tasks.md with numbered tasks, REQ-* references, and test subtasks — ready for implementation
- **@starterkit.technical-analyst** — Assess technical feasibility and risk for each backlog story — complexity, dependencies, security, estimates, and optimal implementation order
- **@starterkit.testing** — Developer testing mindset: generate use-case and risk-based test ideas, unit test plans, and dev test checklists.

## SDD Workflow

1. `@starterkit.constitution` — Define project principles and stack
2. `@starterkit.specify` — Capture feature requirements
3. `@starterkit.plan` — Generate technical plan
4. `@starterkit.backlog` — Break into stories
5. `@starterkit.tasks` — Expand into implementation tasks
6. `@starterkit.implement` — Implement story by story
7. `@starterkit.review` — QA review

## Project Constitution

See `.starterkit/memory/constitution.md` for project-specific rules and stack details.

## Active Rules

- starterkit-angular-angular-patterns: applies to **/*.ts,**/*.html,**/angular.json,**/tsconfig*.json
- starterkit-blazor-blazor-server: applies to **/*.razor,**/*.razor.cs
- starterkit-common-documentation: applies to **/*.md,**/README*,**/openapi*.yaml,**/openapi*.json
- starterkit-common-output-config: applies to all files
- starterkit-common-sdd-principles: applies to all files
- starterkit-common-sdd-workflow: applies to all files
- starterkit-common-security: applies to all files
- starterkit-common-web-security: applies to **/*.ts,**/*.js,**/*.py,**/*.cs,**/*.html,**/*.razor
- starterkit-dotnet-dotnet-aspnetcore: applies to **/*.cs,**/*.csproj,**/appsettings*.json,**/Program.cs
- starterkit-python-python-api: applies to **/*.py,**/pyproject.toml,**/requirements*.txt
- starterkit-python-python-backend-patterns: applies to **/*.py,**/pyproject.toml,**/Dockerfile,**/.env.example
- starterkit-python-python-data-access: applies to **/alembic/**/*.py,**/migrations/**/*.py,**/*model*.py,**/db/**/*.py
- starterkit-python-python-standards: applies to **/*.py
- starterkit-razor-razor-pages: applies to **/*.cshtml,**/*.cshtml.cs
