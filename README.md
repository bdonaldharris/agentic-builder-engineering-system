# agentic-builder-engineering-system

A system of engineering practices, standards, architecture, roles, contracts, and workflows for responsible software construction with AI agents.

## Skills

Canonical reusable engineering procedures live under `skills/`.

- [`application-implementation-workflow`](skills/application-implementation-workflow/SKILL.md) — Governs the implementation lifecycle for application builds: Agent #1 implementation, Agent #2 independent review, correction/re-review, commit/push/integration PR only after approval, mandatory Promotion Protection, and intentionally boring promotion.
- [`engineering-discovery-workflow`](skills/engineering-discovery-workflow/SKILL.md) — Governs evidence-based investigation of existing software systems before implementation, including capability classification, cross-layer tracing, gap analysis, and issue-ready discovery reporting without changing the system.
- [`implementation-readiness-workflow`](skills/implementation-readiness-workflow/SKILL.md) — Determines whether a defined change is sufficiently understood to implement responsibly by resolving material architectural, contract, data, authorization, integration, validation, and scope questions before coding begins.

Application engineering should follow the relevant skill rather than relying on conversational memory or ad hoc prompt reconstruction.
