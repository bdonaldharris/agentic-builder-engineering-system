# agentic-builder-engineering-system

A system of engineering practices, standards, architecture, roles, contracts, and workflows for responsible software construction with AI agents.

## Skills

Canonical reusable engineering procedures live under `skills/`.

- [`application-implementation-workflow`](skills/application-implementation-workflow/SKILL.md) — Governs the implementation lifecycle for application builds: Agent #1 implementation, separate Agent #2 review, correction/re-review, integration PR flow, risk classification/disposition, Promotion Protection, and intentionally boring promotion.
- [`engineering-discovery-workflow`](skills/engineering-discovery-workflow/SKILL.md) — Governs evidence-based investigation of existing software systems before implementation, including capability classification, cross-layer tracing, gap analysis, and issue-ready discovery reporting without changing the system.
- [`implementation-readiness-workflow`](skills/implementation-readiness-workflow/SKILL.md) — Determines whether a defined change is sufficiently understood to implement responsibly by resolving material architectural, contract, data, authorization, integration, validation, and scope questions before coding begins.
- [`independent-implementation-review`](skills/independent-implementation-review/SKILL.md) — Defines the evidence-driven Agent #2 review procedure, including complete-artifact approval, correction convergence, risk classification, and explicit separation between findings and workflow disposition.
- [`integration-pr-review`](skills/integration-pr-review/SKILL.md) — Governs integration PR review and merge gating, including Integration Blocker vs. Production Blocker classification, stable-final-head automated review, correction convergence, human approval, and production-promotion risk evaluation.

Application engineering should follow the relevant skill rather than relying on conversational memory or ad hoc prompt reconstruction.
