# ABES Initial Excavation Inventory

## Purpose

This document is an excavation artifact, not a finished standard, architecture, workflow specification, or constitution.

Its purpose is to capture the engineering ideas that have already emerged through practice and discussion, distinguish their current maturity, and prevent ABES from prematurely canonizing concepts that are still being explored.

The inventory uses five statuses:

- **Established practice** — already used repeatedly as part of the working engineering process.
- **Evidence-backed candidate standard** — derived from concrete engineering failures or review findings and strong enough to consider for formal adoption.
- **Emerging concept** — supported by experience or repeated discussion but not yet sufficiently resolved.
- **Open question** — important, unresolved design or governance question.
- **Historical / foundational hypothesis** — an earlier model that helped shape ABES but may have been refined or superseded.

## Source Threads

### Source A — Early AI Engineering Operating Model

The first parked operating-model discussion proposed explicit roles for architecture, execution readiness, implementation, peer review, and adversarial validation. Its central insight was that pull requests should not be the first place serious engineering review begins.

### Source B — Operating Model + Feature Completion Gate

The second parked discussion preserved the early role model while introducing a new problem: technically correct software can still be the wrong product. It explored whether a separate product-intent alignment responsibility or gate was missing between technical correctness and feature completion.

### Source C — FlashNotes Engineering Standards Excavation

The third parked discussion derived reusable standards from a concrete implementation and release cycle. It produced evidence around single authority, lifecycle timing, stale callbacks, state-machine review, mutation verification, evidence boundaries, and promotion protection.

### Source D — Responsible Agentic Development / Governing-System Discussion

Later discussion began questioning whether the so-called orchestrator is merely a bounded runtime coordinator. The emerging possibility is that the governing system may exist before implementation begins and define roles, rules, contracts, allowed systems, responsibilities, and operating boundaries for the rest of the engineering environment. This remains unresolved and must not yet be treated as canonical architecture.

---

# Inventory

## 1. Explicit standards over tribal knowledge

**Status:** Established practice

**Source / evidence:** Sources A, B, and C; repeated use across current software projects.

**Current understanding:** Important engineering expectations should be made explicit so that human and AI engineers can operate from the same source of truth rather than relying on memory, chat history, or undocumented convention.

**Refined by:** The creation of ABES itself. The current weakness is not absence of standards but lack of a canonical, versioned home for them.

**Candidate ABES layer:** Principles / governance

**Notes / open questions:** Determine which expectations belong in universal ABES standards versus project-specific adoption material.

---

## 2. Architecture before implementation

**Status:** Established practice

**Source / evidence:** Sources A and B; reinforced by subsequent project work.

**Current understanding:** Non-trivial changes should be reasoned about before implementation begins. Scope, boundaries, dependencies, risks, contracts, and intended behavior should be understood before an implementation agent starts changing code.

**Refined by:** Later experience showing that architecture review must include product intent, operational implications, invariants, and evidence requirements, not only code structure.

**Candidate ABES layer:** Lifecycle / architecture

**Notes / open questions:** The depth of architectural reasoning should remain proportional to feature risk and complexity.

---

## 3. PRs should not be the first serious engineering review

**Status:** Established practice

**Source / evidence:** Source A; subsequently operationalized in the Agent 1 → Agent 2 workflow.

**Current understanding:** Implementation should receive independent engineering review before the formal staging/integration PR becomes the primary review surface.

**Refined by:** The current workflow performs review on uncommitted implementation before commit, push, and PR creation.

**Candidate ABES layer:** Review lifecycle

**Notes / open questions:** Earlier five-role formulations should not be canonized merely because this principle survived. The principle is stronger and more durable than the original role names or tool assignments.

---

## 4. Independent implementation and review authority

**Status:** Established practice

**Source / evidence:** Source C and current project workflow.

**Current understanding:** The implementation agent does not approve its own work. A separate reviewer independently examines the uncommitted implementation, returns legitimate findings, and re-reviews corrections before the implementation is committed and pushed.

**Refined by:** Multiple review cycles showing that independence is valuable not merely for code quality but for challenging assumptions, contracts, lifecycle behavior, and test credibility.

**Candidate ABES layer:** Roles / review governance

**Notes / open questions:** ABES should define responsibilities and authority boundaries without unnecessarily binding them to specific vendor tools or fixed agent names.

---

## 5. Correction followed by independent re-review

**Status:** Established practice

**Source / evidence:** Source C and current project workflow.

**Current understanding:** Addressing reviewer findings does not itself restore approval. Corrections are subject to another independent review before the implementation may proceed.

**Refined by:** Real implementation cycles in which fixes introduced new edge cases or changed the system model.

**Candidate ABES layer:** Review lifecycle

**Notes / open questions:** Determine whether re-review requirements can ever be safely abbreviated for trivial findings without weakening independence.

---

## 6. Staging as the deep implementation gate

**Status:** Established practice

**Source / evidence:** Source C and current release workflow.

**Current understanding:** The integration/staging PR is the deep implementation gate. It should validate the implementation, integration, contracts, tests, documentation, and operational behavior before production promotion is considered.

**Refined by:** Promotion Protection, which distinguishes implementation soundness from release-artifact integrity.

**Candidate ABES layer:** Release lifecycle

**Notes / open questions:** Project topology may vary, so ABES may need to define the responsibility of this gate rather than require a branch literally named `staging`.

---

## 7. Promotion should be intentionally boring

**Status:** Established practice

**Source / evidence:** Source C and repeated release practice.

**Current understanding:** Promotion from the reviewed integration artifact to production should introduce no new implementation, no surprise scope, and no avoidable new engineering information.

**Refined by:** Promotion Protection and progressive uncertainty reduction.

**Candidate ABES layer:** Release principles

**Notes / open questions:** A formal definition of “boring” is still needed. A strong candidate is: no material engineering finding should first emerge during promotion if it could reasonably have been discovered earlier.

---

## 8. Progressive uncertainty reduction

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C synthesis.

**Current understanding:** Engineering gates should not merely catch defects independently. Each stage should reduce the categories of uncertainty and surprise available to the next stage.

Implementation reduces construction uncertainty. Independent review attacks assumptions and invariants. Staging establishes integration behavior. Promotion Protection attacks release uncertainty. Promotion should become confirmation rather than discovery.

**Refined by:** “Boring promotion” as an outcome rather than a slogan.

**Candidate ABES layer:** Core principles / lifecycle philosophy

**Notes / open questions:** This may become one of ABES’s highest-level organizing principles.

---

## 9. Single authority / single source of truth

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; UI/controller disagreement during FlashNotes voice dictation.

**Current understanding:** When two values must always agree, first question whether they should actually exist as two independently maintained representations. Prefer one authoritative source with derived consumers over synchronization machinery between competing sources of truth.

**Refined by:** Explicit authority modeling.

**Candidate ABES layer:** Architecture / state design

**Notes / open questions:** Universal principle, with especially high value in stateful systems.

---

## 10. Explicit authority modeling

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C.

**Current understanding:** Systems should make clear which component owns a state transition, lifecycle decision, or mutation. Other representations should derive from that authority rather than compete with it.

**Refined by:** Authority-before-cleanup and stale-work isolation.

**Candidate ABES layer:** Architecture / contracts

**Notes / open questions:** This principle may generalize beyond state ownership into agent authority and system governance.

---

## 11. Lifecycle timing is part of correctness

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; cleanup occurring eventually but observably later than the UI transition.

**Current understanding:** Correctness includes when an invariant becomes true, not merely whether it eventually becomes true. Engineers should distinguish event-synchronous, commit-synchronous, before-observation/paint, and eventual obligations where timing affects correctness.

**Refined by:** Separation of logical state, external lifecycle state, and physical resource state.

**Candidate ABES layer:** Implementation / lifecycle

**Notes / open questions:** Conditional rigor. Especially applicable to stateful, asynchronous, UI lifecycle, and resource-owning features.

---

## 12. Revoke authority before unreliable cleanup

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; browser-owned microphone resource behavior.

**Current understanding:** Logical authority should be revoked before invoking cleanup APIs that may throw, re-enter synchronously, dispatch callbacks later, or fail to physically release a resource. Correctness should not depend on cleanup succeeding perfectly.

**Refined by:** Stale callbacks must be powerless.

**Candidate ABES layer:** Implementation / resource lifecycle

**Notes / open questions:** Conditional standard for externally controlled resources, subscriptions, streams, workers, sockets, observers, timers, SDK sessions, and similar systems.

---

## 13. Stale or superseded work must be powerless

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C.

**Current understanding:** Callbacks or results from terminated, superseded, canceled, or unauthorized work must not be able to mutate current authoritative state.

**Refined by:** Explicit session authority and lifecycle ownership.

**Candidate ABES layer:** Implementation / concurrency / async safety

**Notes / open questions:** Likely broadly applicable to asynchronous and session-oriented systems.

---

## 14. Separate application state, external lifecycle state, and physical state

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; iOS PWA microphone indicator behavior.

**Current understanding:** Application state, browser or external API lifecycle state, and operating-system or physical resource state are different domains. Evidence about one does not automatically prove another.

**Refined by:** Evidence-bounded claims and layer-appropriate verification.

**Candidate ABES layer:** Architecture / verification

**Notes / open questions:** Conditional standard for browser, device, platform, hardware, distributed, or externally owned resources.

---

## 15. Preserve the latest authoritative user-visible value

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; interim dictation text disappeared when terminal settlement trusted only browser-finalized speech.

**Current understanding:** Once the application has authoritatively displayed or accepted a value as belonging to the current session, reconciliation with an external system should not silently regress that value merely because the external system has a narrower notion of finality.

**Refined by:** Application authority taking precedence over incomplete external finalization semantics.

**Candidate ABES layer:** Product behavior / async state

**Notes / open questions:** Conditional standard for streaming, optimistic, user-input, and asynchronous reconciliation workflows.

---

## 16. Review state machines, not only happy paths

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; explicit review of start, failure, duplicate events, delayed termination, stale callbacks, reset, repeated sessions, navigation, replacement sessions, and related transitions.

**Current understanding:** Stateful or asynchronous features should be reviewed across meaningful transitions, terminal paths, failure paths, reentrancy, repetition, stale events, and replacement scenarios rather than only the nominal user journey.

**Refined by:** Risk-proportional review depth.

**Candidate ABES layer:** Review / testing

**Notes / open questions:** ABES should require derivation of feature-relevant transitions, not prescribe a universal checklist copied from one microphone feature.

---

## 17. Prefer invariant-strengthening fixes over symptom patches

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; multiple defects were resolved by changing the underlying authority or state model rather than patching individual symptoms.

**Current understanding:** When a defect reveals a broader class of failure, prefer correcting the governing invariant or model so the whole class becomes harder to reintroduce.

**Refined by:** Regression sensitivity verification.

**Candidate ABES layer:** Architecture / review

**Notes / open questions:** Likely universal, while the cost of redesign should remain proportional to the consequence and recurrence risk.

---

## 18. Regression sensitivity verification

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; deliberate temporary mutations that restored known defects and verified tests failed.

**Current understanding:** For important invariants or previously discovered defect classes, verify when practical that violating the invariant causes the expected tests to fail. Passing tests alone do not prove that the suite can detect the regression it claims to protect.

**Refined by:** Evidence hierarchy.

**Candidate ABES layer:** Testing

**Notes / open questions:** Threshold remains unresolved. This should not automatically be mandatory for every trivial change.

---

## 19. Evidence-bounded claims

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C.

**Current understanding:** A validation artifact may support only claims within the layer it can observe. Source inspection, unit tests, generated artifacts, independent review, staging behavior, and physical-device testing establish different kinds of evidence.

**Refined by:** Layer-appropriate verification and explicit acceptance of unknown/not-verifiable-at-this-layer results.

**Candidate ABES layer:** Verification / testing / review

**Notes / open questions:** Likely universal.

---

## 20. Layer-appropriate verification

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C.

**Current understanding:** Verification should occur at the boundary capable of observing the claimed behavior. Repository logic, generated artifacts, browser integration, external services, and physical devices may require different validation methods.

**Refined by:** Evidence-bounded claims.

**Candidate ABES layer:** Verification

**Notes / open questions:** Terminology may need refinement, but the underlying principle is strong.

---

## 21. Unknown is an acceptable engineering result

**Status:** Evidence-backed candidate standard

**Source / evidence:** Source C; repository evidence could establish application behavior but not conclusively prove iOS physical microphone indication.

**Current understanding:** Engineers and agents should not convert absence of evidence into certainty. “Verified,” “not verified,” and “not verifiable at this layer” are all legitimate outcomes.

**Refined by:** Evidence-bounded claims.

**Candidate ABES layer:** Verification / reporting

**Notes / open questions:** This may become a broader anti-overclaiming standard across ABES.

---

## 22. Promotion Protection

**Status:** Evidence-backed candidate standard with established practice

**Source / evidence:** Source C; repeated use before promotion.

**Current understanding:** Promotion Protection is the deliberate effort to remove foreseeable promotion-stage findings before the promotion PR exists.

It examines the exact effective integration → production artifact for release integrity, preservation of reviewed work, unrelated scope, validation on the promotion artifact, invariant preservation, predictable reviewer findings, and remaining evidence gaps.

**Refined by:** The distinction between implementation review and release-artifact review.

**Candidate ABES layer:** Release / promotion

**Notes / open questions:** Ownership is not yet fully formalized. It may be a stage responsibility rather than a permanent named agent role.

---

## 23. Product intent alignment / Feature Completion Gate

**Status:** Emerging concept

**Source / evidence:** Source B; technically correct FlashNotes implementation still required product-experience refinement.

**Current understanding:** There is a real failure mode between “the code works” and “the thing we built is actually right.” Technical review does not automatically establish that the implementation matches intended product behavior or workflow.

**Refined by:** Later practical product reviews, but no final ABES ownership model has been established.

**Candidate ABES layer:** Product governance / completion criteria

**Notes / open questions:** Do not create a separate permanent role or gate solely because the responsibility exists. Determine whether this belongs to the builder/product owner, architecture, peer review, or a conditional completion checkpoint.

---

## 24. Risk-proportional rigor

**Status:** Emerging concept

**Source / evidence:** Source C.

**Current understanding:** ABES should preserve one engineering lifecycle while varying the depth of analysis and verification according to feature characteristics and consequence. A trivial presentation change should not receive the same lifecycle audit as a stateful, asynchronous, resource-owning, platform-dependent feature.

**Refined by:** Two unresolved approaches: formal risk classes versus trigger-based obligations.

**Candidate ABES layer:** Governance / applicability

**Notes / open questions:** The preferred mechanism is unresolved. Trigger-based rigor may better preserve lightweight operation, but this has not been decided.

---

## 25. Earlier five-agent operating model

**Status:** Historical / foundational hypothesis

**Source / evidence:** Sources A and B.

**Current understanding:** The early model separated Solutions Architect, Engineering Coordinator, Primary Developer, Senior Developer Peer Reviewer, and QA / Adversarial Reviewer responsibilities.

**Refined by:** The later, more operationally proven Agent 1 → Agent 2 → staging → promotion workflow and by emerging questions about product-intent ownership and promotion protection.

**Candidate ABES layer:** Historical provenance; possible source material for future role decomposition

**Notes / open questions:** Do not canonize the five fixed roles or their vendor/tool assignments. Preserve the durable responsibilities and authority boundaries that survived practice.

---

## 26. Tool-independent responsibilities

**Status:** Emerging concept with strong evidence

**Source / evidence:** Sources A through C; tool assignments changed while responsibilities persisted.

**Current understanding:** ABES should define engineering responsibilities, authorities, evidence requirements, and lifecycle boundaries independently of whichever AI model, IDE, agent runtime, or GitHub reviewer happens to perform them.

**Refined by:** The move from named vendor tools toward durable roles and contracts.

**Candidate ABES layer:** Core architecture / roles

**Notes / open questions:** Likely foundational to portability across projects and future tools.

---

## 27. Discover the process before automating it

**Status:** Established principle

**Source / evidence:** Sources A and B; repeated project philosophy.

**Current understanding:** ABES should not automate a workflow before the underlying engineering problem, responsibility, and decision boundary are understood through practice.

**Refined by:** Current decision to excavate ABES before designing its CLI or executable layer.

**Candidate ABES layer:** Core principles

**Notes / open questions:** This should constrain future automation work so tooling remains an execution surface over the engineering model rather than becoming the model itself.

---

## 28. Canonical, versioned engineering system

**Status:** Emerging concept now entering implementation

**Source / evidence:** Current ABES repository creation.

**Current understanding:** The engineering model should no longer depend on essays, chat threads, project-specific prompts, and human memory. ABES should become the durable, inspectable, portable source of truth from which projects and agents consume engineering expectations.

**Refined by:** Future repository structure and adoption model.

**Candidate ABES layer:** Meta / system architecture

**Notes / open questions:** Repository structure should emerge from the excavated concepts rather than be imposed prematurely.

---

## 29. Executable engineering model / CLI

**Status:** Emerging concept

**Source / evidence:** Prior discussion about packaging the engineering system into tooling.

**Current understanding:** ABES may eventually expose executable commands, validation routines, scaffolding, agent instructions, or a CLI. Those mechanisms should be projections of the canonical engineering model, not substitutes for it.

**Refined by:** The decision to canonicalize the model before implementing tooling.

**Candidate ABES layer:** Tooling / automation

**Notes / open questions:** No CLI design should be considered canonical yet.

---

## 30. Governing system / orchestrator

**Status:** Open question / emerging architecture

**Source / evidence:** Source D.

**Current understanding:** The term “orchestrator” may be too narrow. The emerging idea may represent a governing system that exists before implementation begins and establishes roles, rules, contracts, allowed systems, responsibilities, and operating boundaries for the engineering environment.

It may be less like a bounded coordinator that is invoked during construction and more like the foundational system under which construction is permitted to occur.

**Refined by:** Ongoing discussion only.

**Candidate ABES layer:** System architecture / governance

**Notes / open questions:** This must remain explicitly unresolved. Key questions include:

- Is this one component or the governing architecture of ABES itself?
- Is it persistent, instantiated per project, or both?
- Does it define agents, consume agent definitions, or merely enforce their contracts?
- What authority does the human builder retain that this system may never assume?
- Which rules are static, contextual, project-specific, or dynamically derived?
- What evidence must it collect or preserve?
- Is “orchestrator” still an accurate name?

---

## 31. Builder authority

**Status:** Emerging foundational concept

**Source / evidence:** ABES naming discussion and Source D.

**Current understanding:** “Builder” is intentionally present in Agentic Builder Engineering System because agents and automation operate within a human-governed engineering system rather than replacing the builder’s authority over product intent, engineering judgment, and consequential decisions.

**Refined by:** The governing-system discussion.

**Candidate ABES layer:** Core principles / governance

**Notes / open questions:** ABES still needs to define which authorities are delegable, which require human approval, and which must always remain with the builder.

---

# Cross-Cutting Observations

## The model has evolved from roles toward authorities, invariants, evidence, and lifecycle

The earliest operating-model discussions were primarily organized around named agents and review stages. Later project experience shifted the center of gravity toward deeper engineering concepts: who has authority, which invariants must hold, what evidence can establish, how stale work is neutralized, and how uncertainty should decrease across the lifecycle.

This suggests that ABES should probably not be architected primarily as a catalog of agent personas.

## Established workflow and candidate standards are different things

The current Agent 1 → Agent 2 → staging → promotion workflow is operationally proven. Many FlashNotes lessons are candidate standards that enrich what those stages examine. ABES should not accidentally replace a proven workflow with a more elaborate historical role model simply because the older model contains more named boxes.

## Promotion Protection appears to be a distinct responsibility

Implementation review asks whether the change is sound. Promotion Protection asks whether the exact artifact about to ship is still the sound thing that was reviewed, integrated without surprise, and stripped of reasonably foreseeable release findings.

That distinction has survived enough real use to warrant formal exploration.

## Product correctness remains less resolved than technical correctness

ABES has stronger evidence for implementation, review, testing, and promotion standards than it currently has for the ownership of product-intent alignment. The Feature Completion Gate should remain an active design question rather than being automatically turned into another mandatory approval stage.

## The governing-system question could reshape the architecture

The emerging orchestrator discussion may eventually affect how roles, contracts, workflows, evidence, and automation are represented across ABES. Because the concept is still being discovered, repository architecture should not be designed around it yet.

---

# Immediate Open Questions

1. Should ABES use formal risk classes, feature-characteristic triggers, or another mechanism for proportional rigor?
2. Who owns Promotion Protection: Agent 2, a separate reviewer, or the stage itself?
3. What unresolved evidence is allowed at promotion, and what evidence gap must block release?
4. When is regression sensitivity / mutation verification mandatory?
5. Should non-trivial work explicitly declare invariants before implementation?
6. What is the formal definition of an intentionally boring promotion?
7. Where does product-intent alignment belong without introducing unnecessary bureaucracy?
8. Which engineering authorities may agents exercise independently, which require review, and which must remain with the builder?
9. What is the governing/orchestrating system actually responsible for, and is “orchestrator” the right name?
10. Which parts of ABES should become executable, and only after which parts are stable enough to automate?

---

# Excavation Rule

No concept in this inventory becomes canonical ABES doctrine merely because it appears here.

The next step is to critique, merge, split, reject, or promote these concepts based on engineering evidence and builder judgment. Repository architecture, formal standards, agent contracts, and executable tooling should follow that work rather than precede it.
