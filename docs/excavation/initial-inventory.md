# ABES Initial Excavation Inventory

## Purpose

This document is an excavation artifact, not a finished standard, architecture, workflow specification, or constitution.

Its purpose is to capture engineering ideas that have already emerged through practice and reasoning, distinguish their current maturity, and prevent ABES from prematurely canonizing concepts that are still being explored.

The inventory uses five statuses:

- **Established practice** — already used repeatedly as part of the working engineering process.
- **Evidence-backed candidate standard** — derived from concrete engineering failures or review findings and strong enough to consider for formal adoption.
- **Emerging concept** — supported by experience or repeated reasoning but not yet sufficiently resolved.
- **Open question** — important, unresolved design or governance question.
- **Historical / foundational hypothesis** — an earlier model that helped shape ABES but may have been refined or superseded.

---

# Inventory

## 1. Explicit standards over tribal knowledge

**Status:** Established practice

**Current understanding:** Important engineering expectations should be made explicit so human and AI engineers can operate from the same source of truth rather than memory or undocumented convention.

**Refined by:** Creation of ABES. The current weakness is not absence of standards but lack of a canonical, versioned home for them.

**Candidate ABES layer:** Principles / governance

**Notes / open questions:** Determine which expectations belong in universal ABES standards versus project-specific adoption material.

---

## 2. Architecture before implementation

**Status:** Established practice

**Current understanding:** Non-trivial changes should be reasoned about before implementation. Scope, boundaries, dependencies, risks, contracts, and intended behavior should be understood before an implementation agent changes code.

**Refined by:** Experience showing architecture reasoning must include product intent, operational implications, invariants, and evidence requirements, not only code structure.

**Candidate ABES layer:** Lifecycle / architecture

**Notes / open questions:** Depth should remain proportional to feature risk and complexity.

---

## 3. PRs should not be the first serious engineering review

**Status:** Established practice

**Current understanding:** Implementation should receive independent engineering review before the formal staging/integration PR becomes the primary review surface.

**Refined by:** The current workflow performs review on uncommitted implementation before commit, push, and PR creation.

**Candidate ABES layer:** Review lifecycle

**Notes / open questions:** The principle is more durable than any particular role name or tool assignment.

---

## 4. Independent implementation and review authority

**Status:** Established practice

**Current understanding:** The implementation agent does not approve its own work. A separate reviewer independently examines the implementation and re-reviews corrections before commit and push.

**Refined by:** Review cycles showing that independence challenges assumptions, contracts, lifecycle behavior, and test credibility.

**Candidate ABES layer:** Roles / review governance

**Notes / open questions:** Define responsibilities and authority boundaries without binding them to vendors or fixed agent names.

---

## 5. Correction followed by independent re-review

**Status:** Established practice

**Current understanding:** Addressing reviewer findings does not itself restore approval. Corrections require independent re-review before proceeding.

**Refined by:** Real implementation cycles in which fixes introduced new edge cases or changed the system model.

**Candidate ABES layer:** Review lifecycle

**Notes / open questions:** Determine whether trivial findings can ever safely use abbreviated re-review.

---

## 6. Staging as the deep implementation gate

**Status:** Established practice

**Current understanding:** The integration/staging gate validates implementation, integration, contracts, tests, documentation, and operational behavior before production promotion.

**Refined by:** Promotion Protection, which distinguishes implementation soundness from release-artifact integrity.

**Candidate ABES layer:** Release lifecycle

**Notes / open questions:** Define the responsibility of the gate without requiring a literal branch named `staging`.

---

## 7. Promotion should be intentionally boring

**Status:** Established practice

**Current understanding:** Promotion from the reviewed integration artifact should introduce no new implementation, surprise scope, or avoidable new engineering information.

**Refined by:** Promotion Protection and progressive uncertainty reduction.

**Candidate ABES layer:** Release principles

**Notes / open questions:** A formal definition of “boring” remains to be established.

---

## 8. Progressive uncertainty reduction

**Status:** Evidence-backed candidate standard

**Current understanding:** Each engineering stage should reduce the categories of uncertainty and surprise available to the next stage. Promotion should become confirmation rather than discovery.

**Refined by:** The relationship between staged gates and intentionally boring promotion.

**Candidate ABES layer:** Core principles / lifecycle philosophy

**Notes / open questions:** May become a high-level organizing principle for ABES.

---

## 9. Single authority / single source of truth

**Status:** Evidence-backed candidate standard

**Current understanding:** When two values must always agree, first ask whether they should actually exist as two independently maintained representations. Prefer one authoritative source with derived consumers.

**Refined by:** Explicit authority modeling.

**Candidate ABES layer:** Architecture / state design

**Notes / open questions:** Universal principle, especially valuable in stateful systems.

---

## 10. Explicit authority modeling

**Status:** Evidence-backed candidate standard

**Current understanding:** Systems should make clear which component owns a state transition, lifecycle decision, or mutation. Other representations should derive from that authority rather than compete with it.

**Refined by:** Authority-before-cleanup and stale-work isolation.

**Candidate ABES layer:** Architecture / contracts

**Notes / open questions:** May generalize from application state ownership to agent and system governance.

---

## 11. Lifecycle timing is part of correctness

**Status:** Evidence-backed candidate standard

**Current understanding:** Correctness includes when an invariant becomes true, not merely whether it eventually becomes true. Timing obligations may be event-synchronous, commit-synchronous, before-observation/paint, or eventual.

**Refined by:** Separation of logical, external lifecycle, and physical resource state.

**Candidate ABES layer:** Implementation / lifecycle

**Notes / open questions:** Conditional rigor for stateful, asynchronous, UI-lifecycle, and resource-owning features.

---

## 12. Revoke authority before unreliable cleanup

**Status:** Evidence-backed candidate standard

**Current understanding:** Logical authority should be revoked before cleanup APIs that may throw, re-enter, dispatch callbacks later, or fail to physically release a resource.

**Refined by:** Stale-work isolation.

**Candidate ABES layer:** Implementation / resource lifecycle

**Notes / open questions:** Applies conditionally to externally controlled resources, subscriptions, streams, workers, sockets, observers, timers, SDK sessions, and similar systems.

---

## 13. Stale or superseded work must be powerless

**Status:** Evidence-backed candidate standard

**Current understanding:** Callbacks or results from terminated, superseded, canceled, or unauthorized work must not mutate current authoritative state.

**Refined by:** Explicit session authority and lifecycle ownership.

**Candidate ABES layer:** Implementation / concurrency / async safety

**Notes / open questions:** Broadly applicable to asynchronous and session-oriented systems.

---

## 14. Separate application state, external lifecycle state, and physical state

**Status:** Evidence-backed candidate standard

**Current understanding:** Application state, browser/external API lifecycle state, and operating-system/physical resource state are distinct domains. Evidence about one does not automatically prove another.

**Refined by:** Evidence-bounded claims and layer-appropriate verification.

**Candidate ABES layer:** Architecture / verification

**Notes / open questions:** Conditional for browser, device, platform, hardware, distributed, or externally owned resources.

---

## 15. Preserve the latest authoritative user-visible value

**Status:** Evidence-backed candidate standard

**Current understanding:** Once the application has authoritatively displayed or accepted a value as belonging to the current session, external reconciliation should not silently regress it merely because the external system has a narrower notion of finality.

**Refined by:** Application authority taking precedence over incomplete external finalization semantics.

**Candidate ABES layer:** Product behavior / async state

**Notes / open questions:** Conditional for streaming, optimistic, user-input, and asynchronous reconciliation workflows.

---

## 16. Review state machines, not only happy paths

**Status:** Evidence-backed candidate standard

**Current understanding:** Stateful or asynchronous features should be reviewed across meaningful transitions, terminal paths, failure paths, reentrancy, repetition, stale events, and replacement scenarios rather than only the nominal user journey.

**Refined by:** Risk-proportional review depth.

**Candidate ABES layer:** Review / testing

**Notes / open questions:** Derive feature-relevant transitions rather than copying a universal checklist from one feature.

---

## 17. Prefer invariant-strengthening fixes over symptom patches

**Status:** Evidence-backed candidate standard

**Current understanding:** When a defect reveals a broader class of failure, prefer correcting the governing invariant or model so the whole class becomes harder to reintroduce.

**Refined by:** Regression sensitivity verification.

**Candidate ABES layer:** Architecture / review

**Notes / open questions:** Likely universal, with redesign cost proportional to consequence and recurrence risk.

---

## 18. Regression sensitivity verification

**Status:** Evidence-backed candidate standard

**Current understanding:** For important invariants or previously discovered defect classes, verify when practical that violating the invariant causes the expected tests to fail. Passing tests alone do not prove regression sensitivity.

**Refined by:** Evidence-bounded verification.

**Candidate ABES layer:** Testing

**Notes / open questions:** Threshold remains unresolved; should not automatically apply to trivial changes.

---

## 19. Evidence-bounded claims

**Status:** Evidence-backed candidate standard

**Current understanding:** A validation artifact may support only claims within the layer it can observe. Source inspection, tests, generated artifacts, independent review, staging behavior, and physical-device testing establish different kinds of evidence.

**Refined by:** Layer-appropriate verification and explicit acceptance of unknown results.

**Candidate ABES layer:** Verification / testing / review

**Notes / open questions:** Likely universal.

---

## 20. Layer-appropriate verification

**Status:** Evidence-backed candidate standard

**Current understanding:** Verification should occur at the boundary capable of observing the claimed behavior. Repository logic, generated artifacts, browser integration, external services, and physical devices may require different validation methods.

**Refined by:** Evidence-bounded claims.

**Candidate ABES layer:** Verification

**Notes / open questions:** Terminology may need refinement; underlying principle is strong.

---

## 21. Unknown is an acceptable engineering result

**Status:** Evidence-backed candidate standard

**Current understanding:** Engineers and agents should not convert absence of evidence into certainty. “Verified,” “not verified,” and “not verifiable at this layer” are legitimate outcomes.

**Refined by:** Evidence-bounded claims.

**Candidate ABES layer:** Verification / reporting

**Notes / open questions:** May become a broader anti-overclaiming standard.

---

## 22. Promotion Protection

**Status:** Evidence-backed candidate standard with established practice

**Current understanding:** Promotion Protection deliberately removes foreseeable promotion-stage findings before the promotion PR exists. It examines the exact release artifact for integrity, preservation of reviewed work, scope, validation, invariant preservation, predictable findings, and remaining evidence gaps.

**Refined by:** The distinction between implementation review and release-artifact review.

**Candidate ABES layer:** Release / promotion

**Notes / open questions:** Ownership is not yet fully formalized; it may be a stage responsibility rather than a permanent agent role.

---

## 23. Product intent alignment / Feature Completion Gate

**Status:** Emerging concept

**Current understanding:** There is a failure mode between “the code works” and “the thing we built is actually right.” Technical review does not automatically establish alignment with intended product behavior or workflow.

**Refined by:** Practical product review, without a settled ownership model.

**Candidate ABES layer:** Product governance / completion criteria

**Notes / open questions:** Do not create a separate permanent role or gate without further evidence.

---

## 24. Risk-proportional rigor

**Status:** Emerging concept

**Current understanding:** ABES should preserve one engineering lifecycle while varying analysis and verification depth according to feature characteristics and consequence.

**Refined by:** The unresolved choice between formal risk classes and trigger-based obligations.

**Candidate ABES layer:** Governance / applicability

**Notes / open questions:** Mechanism remains unresolved.

---

## 25. Earlier five-agent operating model

**Status:** Historical / foundational hypothesis

**Current understanding:** An early model separated Solutions Architect, Engineering Coordinator, Primary Developer, Senior Developer Peer Reviewer, and QA / Adversarial Reviewer responsibilities.

**Refined by:** The later, more operationally proven Agent 1 → Agent 2 → staging → promotion workflow and emerging questions around product intent and promotion protection.

**Candidate ABES layer:** Historical context only

**Notes / open questions:** Preserve the authority-separation insight where valid; do not canonize the five roles, names, or tool assignments.

---

## 26. Human and AI engineers follow the same engineering discipline

**Status:** Established practice / evidence-backed candidate standard

**Current understanding:** AI agents are engineering participants, not exceptions to engineering discipline. They operate within explicit scope, authority, review, validation, and release controls.

**Refined by:** Independent review and increasingly explicit agent responsibility boundaries.

**Candidate ABES layer:** Core principles / governance

**Notes / open questions:** The precise boundary between human authority and agent execution authority remains part of the governing-system exploration.

---

## 27. Discover the product/process before automating it

**Status:** Established principle

**Current understanding:** Automation should follow demonstrated and understood engineering practice rather than become the mechanism by which the practice is invented. Discover first, formalize second, automate stable portions third.

**Refined by:** The decision to excavate ABES before designing a CLI, orchestrator, or detailed repository architecture.

**Candidate ABES layer:** Core principles / methodology

**Notes / open questions:** Guards against premature tooling and process complexity.

---

## 28. Canonical, versioned engineering system

**Status:** Emerging concept

**Current understanding:** The engineering model has existed across projects, essays, prompts, and learned practices. A canonical repository makes the model persistent, inspectable, versioned, portable, and eventually executable.

**Refined by:** Creation of the ABES repository.

**Candidate ABES layer:** System architecture / governance

**Notes / open questions:** Capture the model before imposing detailed architecture or building a CLI.

---

## 29. ABES as a governing system rather than merely a workflow

**Status:** Emerging concept

**Current understanding:** The model may govern roles, contracts, authority, responsibilities, allowed systems, lifecycle boundaries, and engineering behavior rather than merely orchestrate coding tasks.

**Refined by:** Recognition that the so-called orchestrator may be part of a broader governing system that exists before implementation begins.

**Candidate ABES layer:** System architecture / governance

**Notes / open questions:** Intentionally unresolved. Do not prematurely define orchestrator boundaries, persistence, or implementation architecture.

---

## 30. Governing-system authority must be explicit

**Status:** Open question

**Current understanding:** If ABES becomes a governing system, its authority boundaries must be explicit: what it can prescribe, enforce, validate, delegate, observe, or refuse, and what remains the builder’s responsibility.

**Candidate ABES layer:** Governance / architecture

**Notes / open questions:** Foundational design question; not settled by choosing an orchestrator architecture.

---

## 31. Progressive formalization

**Status:** Emerging concept

**Current understanding:** ABES should move deliberately from observed practice to articulated principles, from principles to standards, and from stable standards to executable mechanisms where appropriate. Not every concept needs immediate automation.

**Refined by:** The excavation-first approach and resistance to prematurely building a CLI or adopting third-party specification tooling.

**Candidate ABES layer:** Methodology / system evolution

**Notes / open questions:** Determine when a practice has enough evidence and stability to become normative or executable.


---

## 32. Separate agent collaboration context from customer UI

**Status:** Evidence-backed candidate standard

**Current understanding:** Agent-readable collaboration context should use explicit non-customer-facing channels—repository structure, component names, structured metadata, project instructions, comments, test identifiers, and authorized tools. Customer-visible UI is product content and requires a production-readiness audit for annotations, placeholder copy, mock data, debug controls, and other prototype residue.

**Refined by:** Investigation of the unsupported claim that agents deliberately render visible UI metadata to preserve their own memory. Rendering an identifier does not create a special agent-memory channel; source and tool context are the relevant mechanisms.

**Candidate ABES layer:** Agent context design / product completion / verification

**Notes / open questions:** Preserve accessibility labels, customer-relevant status, useful diagnostics, and error handling; cleanup must not become indiscriminate deletion.

---

## 33. Mechanism-specific source verification

**Status:** Evidence-backed candidate standard

**Current understanding:** A citation is adequate only when it supports the exact causal mechanism asserted. Sources that share terms or discuss an adjacent topic do not establish a narrower behavioral claim. Material claims about agent behavior should identify the precise mechanism, the system boundary, and an exact supporting quotation or primary source.

**Refined by:** A source audit in which real material on context engineering, machine-readable design systems, and feature-flag cleanup did not support a claim that agents intentionally render metadata in customer UI to aid their own memory.

**Candidate ABES layer:** Verification / review / governance

**Notes / open questions:** This operationalizes evidence-bounded claims. “Not documented” and “not verifiable from the available evidence” remain valid engineering results.
