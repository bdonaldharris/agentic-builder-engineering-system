# agentic-builder-engineering-system

A system of engineering practices, standards, architecture, roles, contracts, and workflows for responsible software construction with AI agents.

## Skills

Canonical reusable engineering procedures live under `skills/`.

- [`application-implementation-workflow`](skills/application-implementation-workflow/SKILL.md) — Governs scoped application implementation: inspect existing architecture, clarify material ambiguity, Agent #1 implementation, separate Agent #2 review, issue-bound corrections, one normal automated integration-PR review, and intentionally boring promotion.
- [`engineering-discovery-workflow`](skills/engineering-discovery-workflow/SKILL.md) — Governs evidence-based investigation of existing software systems before implementation, including capability classification, cross-layer tracing, gap analysis, and issue-ready discovery reporting without changing the system.
- [`implementation-readiness-workflow`](skills/implementation-readiness-workflow/SKILL.md) — Determines whether a defined change is sufficiently understood to implement responsibly by resolving material architectural, contract, data, authorization, integration, validation, and scope questions before coding begins.
- [`independent-implementation-review`](skills/independent-implementation-review/SKILL.md) — Defines Agent #2's narrow three-question review contract: acceptance criteria met, no new bug/regression introduced, and implemented behavior works. Non-blocking observations are suggestions, not current blockers.
- [`integration-pr-review`](skills/integration-pr-review/SKILL.md) — Governs integration PR review using the same three-question contract, one normal automated review, independent finding evaluation, narrow correction re-review, and non-recursive merge convergence.

### Review contract

Application implementation review asks:

1. Was the issue acceptance criteria met?
2. Did the implementation introduce a new bug or regression?
3. Does the implemented behavior work?

`Yes -> No -> Yes` means approval. For an integration PR, it means merge once required CI and human approval are satisfied.

Non-blocking review observations belong in the application repository's persistent `Application Improvement Suggestions` backlog rather than automatically becoming standalone issues.

Application engineering should follow the relevant skill rather than relying on conversational memory or ad hoc prompt reconstruction.
