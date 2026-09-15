# Implementation Readiness Workflow

## Purpose

This skill defines the pre-implementation readiness process for application changes constructed with AI agents.

It exists to prevent implementation from beginning before the issue, affected system, architectural boundaries, contracts, risks, and validation surfaces are sufficiently understood.

The goal is not to produce a large design document before every change.

The goal is to establish that the change is understood well enough to implement deliberately rather than discovering the design while modifying the system.

---

# Applicability

Use this skill before implementation when a change is non-trivial, crosses architectural or repository boundaries, introduces or modifies domain behavior, affects external integrations, changes persisted data, touches authorization/security boundaries, or otherwise carries meaningful implementation uncertainty.

A project may skip a separate readiness pass for a small, already-understood change when the implementation agent can demonstrate that the required readiness questions are already resolved from the issue and current repository state.

Skipping a separate readiness artifact does not waive the readiness concerns defined by this skill.

This skill is application-agnostic.

---

# Core invariant

**Do not begin implementation while material design or integration questions remain unresolved.**

Implementation may reveal minor details, but it must not become the primary mechanism for discovering:

- what the issue means;
- where the behavior belongs;
- which existing system owns the responsibility;
- what contracts must change;
- how authorization or persistence should work;
- which repositories or layers are affected;
- how the change will be validated.

If a material unknown prevents responsible implementation, stop and resolve it before coding.

---

# Relationship to discovery

Implementation readiness is not the same as engineering discovery.

Use `engineering-discovery-workflow` when the purpose is to investigate an existing system, determine current capability, or answer unresolved questions about how the system behaves.

Use this skill when the governing issue is sufficiently defined and the question is:

> Is this change understood well enough to implement responsibly?

If readiness inspection exposes a material unknown about the current system that cannot be resolved with focused repository inspection, the change may need a separate discovery effort before implementation proceeds.

Do not hide a discovery project inside an implementation-readiness pass.

---

# Required readiness dimensions

Inspect only the dimensions relevant to the issue, but do not omit a relevant dimension merely because the issue does not mention it explicitly.

## 1. Governing issue and acceptance boundary

Establish:

- the problem being solved;
- the intended user or system behavior;
- explicit acceptance criteria;
- important implied requirements necessary for correctness;
- known exclusions and out-of-scope behavior;
- whether the issue is sufficiently specific to implement.

Do not silently invent missing product behavior.

If the issue requires a product or architectural decision that has not been made, surface the decision rather than choosing arbitrarily.

## 2. Repository and ownership boundary

Determine:

- which repository or repositories are affected;
- which layer or subsystem currently owns the relevant responsibility;
- whether similar behavior already exists;
- which established repository patterns should be followed;
- whether the proposed change would duplicate existing capability.

Prefer extending the established owning system over creating a parallel implementation without justification.

## 3. Domain and architecture impact

Determine whether the change affects:

- domain entities or value objects;
- application/service boundaries;
- commands, queries, handlers, services, or workflows;
- state transitions and lifecycle rules;
- events or asynchronous processing;
- architectural dependency direction;
- public versus internal responsibilities.

Identify the expected architectural placement before implementation begins.

## 4. API and runtime contracts

Where applicable, identify affected:

- HTTP endpoints;
- request/response models;
- OpenAPI contracts;
- validation rules;
- frontend/backend payload assumptions;
- internal service interfaces;
- event/message contracts;
- third-party API contracts.

Do not treat compile-time compatibility as proof of runtime contract compatibility.

## 5. Persistence and data behavior

Where applicable, determine:

- whether persisted data changes;
- schema or migration requirements;
- query/read-model impact;
- transaction boundaries;
- uniqueness and referential constraints;
- backfill or compatibility concerns;
- likely query-volume or N+1 risks;
- idempotency requirements.

## 6. Authorization, security, and privacy

Where applicable, determine:

- who may perform the behavior;
- which resource ownership or role rules apply;
- whether an existing authorization pattern should be reused;
- whether private or sensitive data is introduced or exposed;
- trust boundaries for external integrations;
- whether validation must occur at more than one boundary.

Security-relevant behavior must not be left for implementation-time improvisation.

## 7. External integrations and operational behavior

For email, payments, identity providers, conferencing, AI providers, storage, queues, or other external systems, determine:

- the existing integration surface;
- ownership of credentials/configuration;
- expected success and failure behavior;
- retry, timeout, cancellation, and idempotency concerns where applicable;
- testability and local/staging behavior;
- observability or operational signals needed.

Do not create a second integration path when an established abstraction already exists unless the issue explicitly requires it.

## 8. User experience and client behavior

Where applicable, determine:

- entry points;
- loading, success, empty, and failure states;
- navigation/deep-link behavior;
- authentication continuity;
- accessibility implications;
- responsive behavior;
- whether the requested experience matches established product patterns.

Implementation readiness is not limited to backend correctness.

## 9. Validation surface

Before implementation begins, identify how the change will be proven correct.

This may include:

- unit tests;
- integration tests;
- contract tests;
- end-to-end tests;
- focused manual validation;
- regression suites;
- migration validation;
- external-integration test modes;
- lint/typecheck/build validation.

The readiness pass should identify expected validation categories, not fabricate test implementation details prematurely.

## 10. Adjacent-system and regression risk

Identify behavior likely to be affected indirectly.

Consider:

- shared components or services;
- existing consumers of changed contracts;
- lifecycle transitions;
- caching;
- notifications;
- background jobs;
- authorization assumptions;
- mobile/responsive surfaces;
- administrative behavior;
- public/private content differences.

A narrow issue can still have a broad execution path.

---

# Focused repository inspection

Readiness must be grounded in the actual current repository state.

The agent should inspect the minimum sufficient set of relevant artifacts, which may include:

- existing implementations of similar behavior;
- domain models;
- routes/controllers/endpoints;
- services/handlers;
- repositories/data-access code;
- migrations/schema;
- frontend components/state/data hooks;
- authorization policies;
- integration clients;
- tests;
- API specifications;
- relevant documentation.

Do not infer architecture solely from filenames, issue wording, or an implementation summary from another agent.

---

# Unknowns and decision handling

Classify unresolved items before implementation begins.

## Resolvable implementation detail

A minor detail that can be safely resolved while following an established repository pattern.

This does not block readiness.

## Material unknown

A question whose answer changes architecture, product behavior, contract shape, data semantics, authorization, integration ownership, or other meaningful implementation direction.

This blocks readiness until resolved.

## External decision required

A product, architecture, business, or policy choice that the implementation agent is not authorized to invent.

Stop and surface the decision clearly.

Do not convert an external decision into an agent assumption merely to keep implementation moving.

---

# Scope control

Readiness inspection may expose nearby deficiencies or opportunities.

Separate them into:

- required for the governing issue;
- legitimate prerequisite;
- non-blocking follow-up;
- unrelated.

Do not expand the implementation scope because adjacent cleanup was discovered.

If an adjacent problem materially prevents correct implementation, identify it explicitly as a prerequisite rather than silently absorbing it.

---

# Readiness output

The readiness report should be concise enough to guide implementation but complete enough to prevent major rediscovery during coding.

Use the following structure when appropriate:

```markdown
# Implementation Readiness

## Decision
READY | NOT READY

## Governing Issue
<issue reference and concise objective>

## Acceptance Boundary
- ...

## Existing System / Patterns to Reuse
- ...

## Expected Change Surface
### Repository / Layer
- ...

## Contracts and Data Impact
- ...

## Authorization / Security / Privacy
- ...

## External Integrations / Operational Concerns
- ...

## Validation Plan
- ...

## Regression / Adjacent-System Risks
- ...

## Scope Boundaries
### In scope
- ...

### Out of scope / follow-up
- ...

## Unresolved Questions
- None

—or—

- <material unknown or external decision>

## Implementation Guidance
- <specific existing patterns, files, abstractions, or architectural placement the implementation agent should follow>
```

Do not add empty sections merely for ceremony when a dimension is genuinely irrelevant.

---

# Readiness decision

Return exactly one readiness decision.

## READY

Use `READY` only when:

- the governing issue is sufficiently understood;
- the expected implementation ownership and placement are known;
- material contracts and data effects are understood;
- relevant authorization/security concerns are understood;
- relevant integration concerns are understood;
- a reasonable validation path is known;
- no material unknown or unauthorized decision remains.

`READY` does not mean every line of code has been predetermined.

It means implementation can begin without relying on architectural guesswork.

## NOT READY

Use `NOT READY` when a material unknown, unresolved dependency, missing discovery, or external decision would force the implementation agent to invent meaningful behavior or architecture.

State exactly what must be resolved before implementation begins.

Do not pad a `NOT READY` decision with speculative implementation.

---

# Prohibited behavior

During implementation readiness, do not:

- modify production code;
- implement the feature;
- create speculative abstractions;
- perform opportunistic refactoring;
- invent product requirements;
- invent architectural decisions outside the issue's authority;
- treat assumptions as established facts;
- silently broaden scope;
- produce a large design document when focused readiness evidence is sufficient.

A separate explicit request may authorize issue updates, documentation changes, or other non-implementation actions after the readiness analysis is complete.

---

# Handoff to implementation

When the decision is `READY`, the readiness output becomes context for the implementation agent.

The implementation itself should then follow `application-implementation-workflow`.

The readiness report does not authorize bypassing any implementation hold point, independent review, PR review, or promotion protection defined by that workflow.

If implementation uncovers a material contradiction in the readiness assumptions, stop and resolve the contradiction rather than silently changing direction.

---

# Workflow summary

```text
Defined implementation issue
        ↓
Inspect governing issue + current repository state
        ↓
Trace relevant architecture / contracts / data / auth / integrations / UX
        ↓
Identify validation + regression surface
        ↓
Classify unknowns and scope
        ↓
   Material unknown?
      ├── Yes → NOT READY
      │          ↓
      │    discovery / decision / prerequisite
      │          ↓
      │       re-evaluate
      │
      └── No → READY
                 ↓
       application-implementation-workflow
```

---

# Definition of readiness compliance

A change has passed this skill only when:

- readiness is grounded in the current repository state;
- the issue and acceptance boundary are understood;
- the owning architecture and established patterns have been identified;
- relevant contract, persistence, authorization, integration, UX, and operational impacts have been considered;
- the expected validation surface is known;
- adjacent risks and scope boundaries are explicit;
- material unknowns are resolved rather than converted into assumptions;
- the agent returns an explicit `READY` or `NOT READY` decision;
- no implementation work is performed as part of the readiness pass;
- a `READY` change proceeds through the normal application implementation workflow.
