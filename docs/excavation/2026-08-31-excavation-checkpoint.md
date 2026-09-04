# ABES Excavation Checkpoint — 2026-08-31

## Purpose

This document is an excavation benchmark, not an ABES specification.

It preserves the current state of understanding so the excavation can continue without depending on conversational memory or prematurely turning emerging ideas into system design.

Nothing recorded here becomes a principle, standard, architectural requirement, role, lifecycle rule, or executable mechanism merely by appearing in this document. This checkpoint records what is currently known, suspected, strengthened by evidence, and still unresolved.

The governing discipline remains:

> **Excavate before we architect.**

The existing [initial excavation inventory](initial-inventory.md) captures individual practices, candidate standards, emerging concepts, historical hypotheses, and open questions. This checkpoint serves a different purpose: it records what we currently understand about the body of evidence as a whole and how that evidence should be interpreted going forward.

---

## What We Are Excavating

ABES is not being designed from a blank page.

A recognizable engineering system has been developing through actual software construction, review, failure, correction, release, and reflection across multiple projects. The current work is an attempt to recover that system, distinguish durable engineering knowledge from project-specific adaptations, and eventually unify it into a coherent system without imposing architecture before the evidence supports it.

The goal is not to collect every useful rule from every project into one large standards repository.

The goal is to determine what has been learned about responsible software construction with AI agents, how that understanding evolved, which practices survived repeated use, which were refined or superseded, which remain contextual, and which deeper engineering principles explain the practices that keep appearing.

---

## Project Lineage

The current evidence should be understood as an evolutionary lineage rather than four unrelated project samples:

> **BitVoices → HindSite → FlashNotes → Stewart**

Knowledge, practices, assumptions, and lessons moved forward through this sequence. Later projects therefore cannot be treated as statistically independent confirmation of earlier practices.

The lineage matters because recurrence and evolution answer different questions:

- **Recurrence:** Does a concern continue to matter across different kinds of software?
- **Evolution:** How did the treatment of that concern change as engineering experience accumulated?

Evolution may be as important to ABES as recurrence.

### BitVoices

BitVoices is the earliest major source in the current excavation and contains substantial explicit engineering machinery: specifications, implementation plans, dependency modeling, standards, contribution rules, architecture guidance, testing expectations, CI gates, staging and promotion mechanics, operational documentation, and agent instructions.

The BitVoices backend is also a hybrid. Some of its structure originated in GitHub Spec Kit while personal engineering standards, workflows, review practices, and later lessons accumulated around and beyond that structure.

This means BitVoices artifacts must be interpreted carefully. A mechanism appearing there may be:

- inherited from Spec Kit;
- adapted because it proved useful;
- superseded by later practice;
- project-specific;
- or an early expression of a more general principle that became clearer later.

ABES should not automatically inherit Spec Kit machinery merely because that machinery appears in its ancestry.

### HindSite

HindSite expresses stronger governance around product truth, engineering truth, deterministic versus inferred behavior, documentation authority, issue boundaries, readiness and completion, and bounded AI participation.

It provides evidence that specification alone is insufficient. The engineering environment must establish what is authoritative, where different kinds of truth live, what an agent may determine, what remains outside its authority, what context must be consumed before implementation, and what evidence is required before work is considered complete.

### FlashNotes

FlashNotes applied the accumulated discipline to a much smaller personal application and exposed important defect classes through real feature implementation and repeated independent review.

Those failures sharpened reasoning around authority, lifecycle timing, stale work, controller ownership, mutation boundaries, state-machine review, regression sensitivity, evidence-bounded claims, layer-appropriate verification, product intent, staging, and promotion integrity.

FlashNotes also reinforces that rigor does not require maximal ceremony. A smaller system can obey strong engineering principles through a much lighter project-specific expression.

### Stewart

Stewart is the latest project in the current lineage and introduces an explicitly agentic runtime architecture with a supervising agent, bounded specialists, controlled communication paths, concurrent independent investigations, fan-in before synthesis, session-scoped knowledge, and human creative authority.

Its significance to ABES is still being excavated. It should not be used prematurely as a template for an ABES orchestrator or agent topology simply because it is the newest project or because its runtime is explicitly agentic.

---

## How To Interpret Change Across The Lineage

Newer does not automatically mean better, and different does not automatically mean evolved.

When a practice changes from one project to another, the excavation should ask:

> **Is this an evolution in engineering understanding, or an adaptation to project context?**

A large multi-user platform, a reconstruction-oriented product, a personal field-notes application, and a multi-agent creative system have materially different risk profiles and architectural needs.

A practice disappearing in a later project may therefore mean either:

- experience showed it was unnecessary or inferior; or
- the later project simply did not require that mechanism.

Likewise, a later mechanism may represent a genuine improvement, or merely a context-specific response.

This distinction is essential if ABES is to become unified without becoming rigid.

---

## Current Excavation Lens

A useful way to reason about practices across the lineage is:

> **Origin → Application → Failure / Pressure → Adaptation → Later Expression → Current Hypothesis**

This is an analytical lens, not a required ABES artifact format and not a process that must be completed mechanically for every finding.

Its purpose is to prevent a common excavation error: confusing the existence of a mechanism with evidence that the mechanism itself should become canonical.

For inherited mechanisms in particular, distinguish:

> **Inherited mechanism → adapted practice → discovered principle**

ABES may ultimately need the discovered principle, an evolved mechanism, both, or neither.

The important question is what persisted because it remained useful and what merely existed because of a particular project's tooling, architecture, or stage of maturity.

---

## Current Understanding Of The Emerging System

The evidence increasingly suggests that the implementation workflow is an expression of a deeper engineering system rather than the entirety of that system.

The familiar implementation lifecycle remains important:

> implementation → independent review → correction → independent re-review → integration/staging validation → promotion protection → production promotion

But across the projects, deeper concerns repeatedly appear beneath that lifecycle:

- authority;
- truth and precedence;
- context;
- boundaries;
- evidence;
- uncertainty;
- accountability;
- comprehension;
- proportionality;
- semantic preservation;
- progressive verification;
- and learning from failures.

These are not yet declared ABES foundational principles. They are recurring concerns whose relationships and proper abstraction levels remain under excavation.

---

## Strengthened Hypotheses

The cross-project evidence has strengthened several hypotheses beyond where they stood in the initial inventory. They remain hypotheses until deliberately formalized.

### Authority Is Broader Than A Single Source Of Truth

The phrase "single source of truth" is useful but incomplete.

A system may legitimately contain multiple kinds of truth or authority: product intent, implementation rules, raw observations, canonical representations, inferred interpretations, human-corrected understanding, lifecycle state, configuration state, and runtime state.

The emerging concern is not that an entire system must have one source of truth. It is that consequential decisions should have identifiable authorities and that competing representations should have explicit precedence.

A concern should generally have one authoritative representation at a given level, while the larger system may contain multiple explicitly distinguished authorities for different classes of truth.

### Capability Does Not Imply Authority

An actor, component, or AI model may be technically capable of performing an operation without being authorized to determine the truth or decision represented by that operation.

This distinction appears increasingly important to both software architecture and agent governance.

### Context Is Engineering Infrastructure

Responsible delegated engineering depends on the context made available to the acting engineer or agent.

Across the projects, that context includes combinations of governing product artifacts, architecture, standards, contracts, acceptance criteria, allowed scope, validation expectations, truth boundaries, operational constraints, relevant implementation patterns, and unresolved questions.

Prompting is one delivery mechanism for this context, not the underlying engineering concept.

### Engineering Truth Has Location And Precedence

Different kinds of durable engineering information belong in different authoritative locations.

Product truth, process truth, repository implementation truth, execution scope, architecture rules, design guidance, and runtime configuration need not share one document or representation. What matters is that ownership is explicit and accidental duplication does not create competing authorities.

### Comprehensibility May Be An Engineering Property

Agentic construction can increase implementation speed faster than responsible humans can accumulate understanding.

The projects repeatedly preserve reasoning, governing artifacts, standards, trade-offs, reusable patterns, version history, and decision context rather than relying only on working code.

This suggests a broader concern with comprehension debt: software should not evolve so quickly that the people accountable for it lose sufficient understanding of why the system has its current shape.

### Uncertainty Must Remain Visible Until Evidence Resolves It

The current evidence repeatedly rejects converting missing evidence into certainty.

"Unknown," "not verified," and "not verifiable at this layer" can be legitimate engineering outcomes. Agents should not guess merely to complete a task, weaken semantics to produce a working result, or present inference as established truth.

### Verification Authority Is Bounded

A verification mechanism can support only claims within what it can observe.

Compilation, source inspection, unit tests, integration tests, generated artifacts, browser behavior, external-service behavior, staging behavior, independent review, and physical-device validation establish different kinds of evidence.

Passing one layer does not automatically prove behavior that exists at another.

### Technical Boundaries Should Not Silently Weaken Governing Semantics

Several projects contain cases where moving across an API, persistence, transport, lifecycle, asynchronous, or architectural boundary could accidentally weaken behavior that the system depends upon.

The recurring concern is preservation of governing semantics across those boundaries, even when a simpler implementation would appear to work nominally.

### Responsible Engineering Includes Refusing Unnecessary Engineering

Simplicity is repeatedly treated as an engineering requirement rather than an absence of sophistication.

The projects discourage premature abstraction, speculative infrastructure, unproven shared state, unnecessary feature clients, hypothetical scaling machinery, and architecture created before demonstrated need.

ABES itself should remain subject to this constraint.

### Human Accountability Persists Through Delegation

Execution can be delegated. Ultimate engineering accountability cannot simply disappear because an agent performed the work.

The exact authority model between builder, governing system, implementation agent, review agent, and other participants remains unresolved, but delegated execution does not appear to eliminate human responsibility for direction, judgment, acceptance, and consequences.

### Engineering Compromises Should Be Intentional

Technical debt, staged limitations, temporary architecture, and other compromises can be legitimate when consciously chosen and understood.

The emerging distinction is between deliberate, bounded compromise and accidental degradation discovered later.

### Standards Emerge From Engineering Evidence

The project lineage demonstrates a recurring progression:

> **experience → discovery → explicit reasoning → reusable pattern → standard → enforcement where appropriate**

Standards should therefore be justified by engineering need rather than aesthetic preference or framework completeness.

### Rigor Should Be Proportional

The same underlying engineering discipline can require different mechanisms depending on consequence, uncertainty, system characteristics, and project maturity.

Unified does not mean identical ceremony.

The open problem is determining which obligations are invariant and which expressions should vary by context.

---

## Emerging Distinction: Invariants And Context-Sensitive Expressions

A possible direction is becoming visible, but it is not yet canonical.

ABES may eventually contain a relatively small set of engineering invariants that apply broadly, while allowing projects to satisfy those invariants through context-sensitive mechanisms.

For example, two projects might both require evidence appropriate to a claim while needing entirely different verification suites. Two projects might both require durable governing context while one needs a few concise documents and another needs specialized architecture, API, data, security, and operational standards.

This would allow ABES to remain unified without requiring every project to use the same tooling, document count, branch topology, agent count, or verification depth.

Further excavation is required before deciding whether "invariants and expressions" is the correct model or merely a useful current hypothesis.

---

## Emerging Distinction: ABES Governs Truth Without Supplying Project Truth

Another potentially important distinction is becoming visible.

ABES cannot supply the domain truth, product intent, architecture, or runtime facts of arbitrary software projects. Those truths are project-specific.

What ABES may govern is how builders and agents:

- establish those truths;
- identify their authorities;
- locate and communicate them;
- consume them before acting;
- preserve them across implementation boundaries;
- challenge them when evidence exposes contradictions;
- verify claims about them;
- and update durable engineering knowledge when failures reveal better understanding.

This would make ABES a governing engineering system rather than a repository of universal application architecture rules.

This remains an emerging hypothesis, not a settled definition of ABES.

---

## What Must Not Be Prematurely Canonized

The excavation does not currently justify fixing any of the following as universal ABES architecture:

- a specific orchestrator design;
- a fixed number of agents;
- the earlier five-agent role model;
- Stewart's supervisor/specialist topology;
- a literal `staging` branch;
- GitHub Spec Kit's artifact structure;
- a universal risk-classification scheme;
- a universal document hierarchy;
- a CLI;
- a mandatory implementation technology;
- or a single fixed workflow with identical ceremony for every project.

These may contain useful mechanisms or evidence. They are not automatically the system.

---

## Relationship To The Initial Inventory

The initial inventory remains useful as the item-level excavation record.

This checkpoint does not replace it and should not silently rewrite its statuses.

Several inventory items now have stronger cross-project evidence, and some language — particularly around "single source of truth" — may eventually need refinement. Those changes should occur deliberately after the excavation determines the appropriate abstraction, rather than being retrofitted simply because this checkpoint identified a broader hypothesis.

In short:

- **`initial-inventory.md` asks:** What have we found?
- **This checkpoint asks:** What do we currently understand about what we have found?

---

## Current Open Questions

Important unresolved questions include:

1. What are the true foundational invariants of ABES, if that is the correct abstraction?
2. Which current practices are universal engineering obligations versus context-sensitive expressions?
3. Which BitVoices mechanisms originated primarily from Spec Kit, and which survived because they proved independently valuable?
4. How should ABES distinguish product truth, engineering truth, execution scope, runtime truth, and evidence without creating unnecessary bureaucracy?
5. What exactly remains the builder's authority and accountability when execution, review, verification, or orchestration is delegated?
6. What role, if any, should a persistent governing/orchestrating system eventually play?
7. How should ABES determine the appropriate rigor for a change without introducing heavy risk-classification ceremony?
8. When has a practice accumulated enough evidence and stability to move from excavation into a normative standard?
9. What evidence should be preserved when a standard evolves so later users can understand why it exists?
10. How should projects consume ABES while retaining legitimate project-specific architecture, workflows, and constraints?
11. How should comprehension be preserved without requiring documentation that exceeds its engineering value?
12. Which mechanisms should eventually become executable enforcement and which should remain judgment-based engineering guidance?

---

## Benchmark

At this checkpoint, ABES appears to be larger than an AI-assisted coding workflow and narrower than a universal software architecture.

The evidence suggests an emerging governing engineering system concerned with how software construction authority is delegated, constrained, informed, challenged, verified, promoted, and improved over time.

That understanding is provisional.

The next work should continue excavation rather than force these observations into a finished architecture. The purpose of this checkpoint is to make that continued discovery possible without losing the reasoning that brought the work here.

---

## Parked Emerging Concept: Containerization As A Build System Mental Model

**Status:** Emerging concept for later exploration. This is not a decided architecture, implementation approach, or product direction.

A possible mental model has surfaced for applying containerization concepts to an eventual executable Build System. The idea is not necessarily literal software containers. The useful analogy is that a Build System could package **engineering behavior** into a reproducible, versioned, isolated, and portable environment.

Possible analogies include a base layer containing core engineering doctrine; layered capabilities for particular programs, workflows, or technology stacks; persistent project context analogous to volumes; project-specific configuration analogous to environment variables; engineering gates analogous to health checks; versioned Build System releases; project isolation; portability and upgradeability; executable agents, skills, hooks, workflows, and guardrails; and explicit human-judgment checkpoints where the builder remains authoritative.

An important distinction is emerging between systems that primarily package **AI capability** and a Build System that could package **engineering behavior**, with AI capability operating as one component inside that governed environment.

If the concept proves useful, an executable and reproducible Build System could potentially become foundational infrastructure beneath Build Systems used across the SGPS Hub, cohort, and coaching programs.

There is also a possible future product dimension: the Build System itself could eventually be distributable through a proprietary, open-source, or hybrid model.

These possibilities are being preserved only so they are not lost. They should not currently be interpreted as an invitation to design the system, select container technology, define architecture, name a product, create a roadmap, or choose a distribution model.
