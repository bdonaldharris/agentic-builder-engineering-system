# agentic-builder-engineering-system

A system of engineering practices, standards, architecture, roles, contracts, and workflows for responsible software construction with AI agents.

## Operating model

The engineering system separates responsibilities deliberately:

- **Issue/PR = what + durable workflow history/state** — change-specific requirements, acceptance criteria, scope, constraints, implementation/discovery context, and the structured execution record produced by the agents.
- **Skill = how** — reusable workflow rules, role responsibilities, review boundaries, correction flow, reporting requirements, scope control, and recurring `DO NOT` instructions.
- **Prompt = action/location** — identify what should happen, where, and which skill to use.
- **Agent comments = structured execution state and historical record** — concise workflow-state comments make later agent invocations resumable without reconstructing context from chat history.

The current repository/PR artifact remains authoritative for what actually exists. Workflow-state comments record what agents did, found, and decided; they do not override source state.

When an agent is already operating in the correct repository workspace, ordinary prompts should stay extremely concise and should not duplicate issue content, prior workflow history, or workflow rules.

Examples:

```text
Implement FE #1510 using application-implementation-workflow
Review FE #1510 using indep-review
Correct FE #1510 using application-implementation-workflow
Re-review FE #1510 using indep-review
Assess FE #1510 using readiness-workflow
Discover FE #1510 using eng-discovery
Review PR #1501 using pr-review
```

Structured workflow-state comments use recognizable markers so agents can identify the latest relevant state without assuming the newest GitHub comment is the one that matters:

```text
[AGENT #1 — IMPLEMENTATION]
[AGENT #1 — CORRECTION]
[AGENT #2 — INDEPENDENT REVIEW]
```

Agents should read the governing Issue/PR, identify the latest relevant workflow-state comment for the current phase, inspect the current artifact, perform the skill-defined work, and write the resulting state back to the same Issue/PR.

## Skill shorthand

Shorthand is an invocation alias only. The canonical skill remains the single source of workflow truth.

| Shorthand | Canonical skill |
| --- | --- |
| `implement-workflow` | `application-implementation-workflow` |
| `indep-review` | `independent-implementation-review` |
| `readiness-workflow` | `implementation-readiness-workflow` |
| `eng-discovery` | `engineering-discovery-workflow` |
| `pr-review` | `integration-pr-review` |

## Skills

Canonical reusable engineering procedures live under `skills/`.

- [`application-implementation-workflow`](skills/application-implementation-workflow/SKILL.md) (`implement-workflow`) — Governs scoped Agent #1 implementation and correction, durable Issue/PR workflow-state comments, environment/deployment requirement handoff, independent Agent #2 review, coherent issue-bound corrections, bounded automated PR review, final-head artifact-integrity review, and intentionally boring promotion.
- [`engineering-discovery-workflow`](skills/engineering-discovery-workflow/SKILL.md) (`eng-discovery`) — Governs evidence-based investigation of existing software systems before implementation, including capability classification, cross-layer tracing, gap analysis, and issue-ready discovery reporting without changing the system.
- [`implementation-readiness-workflow`](skills/implementation-readiness-workflow/SKILL.md) (`readiness-workflow`) — Determines whether a defined change is sufficiently understood to implement responsibly by resolving material architectural, contract, data, authorization, integration, validation, and scope questions before coding begins.
- [`independent-implementation-review`](skills/independent-implementation-review/SKILL.md) (`indep-review`) — Defines Agent #2's narrow three-question review contract, complete-artifact review, structured Issue/PR disposition comments, bounded sibling-state review, and independent Codex-finding disposition. Codex findings are inputs; Agent #2 determines whether they require correction, are non-blocking/informational, are invalid, or belong outside the current issue boundary.
- [`integration-pr-review`](skills/integration-pr-review/SKILL.md) (`pr-review`) — Governs production-bound PR review using the same three-question contract, durable PR workflow-state comments, the canonical detailed Codex Integration Review Request, Agent #2 disposition authority, coherent correction/re-review flow, bounded final-head review, Environment / Deployment Requirements gating, and non-recursive merge convergence.

### Review contract

Application implementation review asks:

1. Was the issue acceptance criteria met?
2. Did the implementation introduce a new bug or regression?
3. Does the implemented behavior work?

`Yes -> No -> Yes` means approval. For an integration PR, it means merge once required CI and human approval are satisfied.

Non-blocking review observations belong in the application repository's persistent `Application Improvement Suggestions` backlog rather than automatically becoming standalone issues.

Application engineering should follow the relevant skill and durable GitHub workflow-state record rather than relying on conversational memory or ad hoc prompt reconstruction.
