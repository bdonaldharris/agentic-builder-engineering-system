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

## Agent identity and role boundaries

Agent #1 and Agent #2 are distinct agents.

They are not personas, modes, phases, or interchangeable responsibilities of one agent.

A skill invocation establishes the executing agent's identity for that execution, and that identity remains fixed until the invocation ends.

- **Agent #1 — Implementation Agent** owns implementation, correction, implementation validation, artifact preparation, PR creation/finalization, and other implementation-side production-bound work assigned by the workflow.
- **Agent #2 — Independent Review Agent** owns independent complete-artifact review, disposition of implementation findings, disposition of Codex findings, and approval/rejection under the governing three-question contract.

A transition between Agent #1 and Agent #2 requires a separate agent invocation.

Reaching another agent's responsibility is a **handoff boundary**, not permission for the current agent to assume that role.

The role-specific workflow-state markers are owned by their corresponding agent:

- Agent #1 may write `[AGENT #1 — IMPLEMENTATION]`.
- Agent #1 may write `[AGENT #1 — CORRECTION]`.
- Agent #2 may write `[AGENT #2 — INDEPENDENT REVIEW]`.

An agent must never create a workflow-state comment representing the other agent.

The normal stop/handoff model is:

```text
Agent #1
→ implementation
→ READY FOR AGENT #2 REVIEW
→ STOP

separate Agent #2 invocation
→ independent complete-artifact review
→ APPROVED or CHANGES REQUIRED
→ STOP

if correction is required:

separate Agent #1 invocation
→ correction
→ READY FOR AGENT #2 RE-REVIEW
→ STOP
```

Codex is a review input, not Agent #2. A Codex review does not constitute independent Agent #2 review, Codex severity does not control disposition, and Codex returning no findings does not authorize Agent #1 to declare approval. Only a separately invoked Agent #2 using `independent-implementation-review` may produce the Agent #2 independent-review disposition.

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

- [`application-implementation-workflow`](skills/application-implementation-workflow/SKILL.md) (`implement-workflow`) — Executes as **Agent #1 — Implementation Agent** and governs scoped implementation/correction, durable Issue/PR workflow-state comments, environment/deployment requirement handoff, coherent issue-bound corrections, review handoff boundaries, bounded automated PR review, final-head artifact-integrity review, and intentionally boring promotion.
- [`engineering-discovery-workflow`](skills/engineering-discovery-workflow/SKILL.md) (`eng-discovery`) — Governs evidence-based investigation of existing software systems before implementation, including capability classification, cross-layer tracing, gap analysis, and issue-ready discovery reporting without changing the system.
- [`implementation-readiness-workflow`](skills/implementation-readiness-workflow/SKILL.md) (`readiness-workflow`) — Determines whether a defined change is sufficiently understood to implement responsibly by resolving material architectural, contract, data, authorization, integration, validation, and scope questions before coding begins.
- [`independent-implementation-review`](skills/independent-implementation-review/SKILL.md) (`indep-review`) — Executes as **Agent #2 — Independent Review Agent** and defines the narrow three-question review contract, complete-artifact review, structured Issue/PR disposition comments, bounded sibling-state review, and independent Codex-finding disposition. Agent #2 never performs implementation/correction work and stops with a handoff when changes are required.
- [`integration-pr-review`](skills/integration-pr-review/SKILL.md) (`pr-review`) — Executes as **Agent #1 — Implementation Agent** for PR-side integration/finalization responsibilities. It governs durable PR workflow-state comments, the canonical detailed Codex Integration Review Request, hard Agent #2 disposition handoffs, coherent correction/re-review flow, bounded final-head review, Environment / Deployment Requirements gating, and non-recursive merge convergence.

### Review contract

Application implementation review asks:

1. Was the issue acceptance criteria met?
2. Did the implementation introduce a new bug or regression?
3. Does the implemented behavior work?

`Yes -> No -> Yes` means approval. For an integration PR, it means merge once required CI and human approval are satisfied.

Non-blocking review observations belong in the application repository's persistent `Application Improvement Suggestions` backlog rather than automatically becoming standalone issues.

Application engineering should follow the relevant skill and durable GitHub workflow-state record rather than relying on conversational memory or ad hoc prompt reconstruction.
