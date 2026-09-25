---
name: engineering-discovery-workflow
description: Shorthand: eng-discovery. Investigate an existing software system before implementation. Use for repository discovery, current-state audits, capability inventories, gap analysis, cross-layer tracing, and evidence-backed engineering investigation where the system must not be changed.
---

# Engineering Discovery Workflow

## Purpose

This skill defines the canonical workflow for investigating an existing software system before implementation.

It exists to make discovery evidence-based, repeatable, and clearly separated from implementation. The goal is to understand what the system actually does, identify relevant gaps, and produce findings that can support implementation planning or issue creation without changing the software during discovery.

## Invocation model

The governing issue or discovery request is the authoritative source of the change-specific question, desired behavior, scope, and known context.

This skill owns the reusable discovery procedure. The invocation prompt does not need to restate discovery rules already defined here.

When the agent is already operating in the correct repository workspace, a minimal invocation is sufficient, for example:

```text
discover #1482 using eng-discovery
```

Resolve `eng-discovery` to this canonical skill, retrieve/read the governing issue or request, and investigate the current repository state.

## Applicability

Use this workflow when an issue, task, or engineering question requires investigation of existing system behavior, architecture, capabilities, gaps, or readiness before implementation begins.

Typical uses include:

- auditing an existing capability against a new requirement;
- determining whether functionality already exists;
- tracing backend/frontend behavior across repositories;
- identifying architectural or contract gaps;
- assessing readiness for a larger implementation;
- producing evidence for follow-up implementation issues.

This skill is application-agnostic.

---

# Core invariant

**Discovery investigates. Discovery does not implement.**

A discovery pass must not silently become a coding pass, cleanup pass, refactor, migration, or feature implementation.

If implementation work is identified, capture it as a finding or follow-up issue. Do not perform it unless the governing request explicitly ends discovery and authorizes implementation under the appropriate implementation workflow.

---

# Discovery principles

## 1. Inspect the actual system

Do not infer behavior from issue wording, file names, comments, stale documentation, or prior assumptions when the repository can be inspected directly.

Trace the relevant implementation far enough to understand actual behavior.

Depending on scope, inspect as appropriate:

- domain models;
- API routes and handlers;
- services and orchestration;
- persistence and data access;
- authorization and ownership rules;
- frontend routes, components, and state;
- background jobs and messaging;
- external integrations;
- configuration and feature flags;
- tests;
- migrations;
- OpenAPI or other interface contracts;
- documentation when it helps explain intended behavior.

Documentation may support discovery, but repository/runtime evidence takes precedence when the two disagree.

## 2. Follow execution paths, not just symbols

Finding a class, endpoint, component, or configuration entry is not enough to prove that a capability exists.

Where relevant, trace how the capability is invoked, what data it reads or writes, what conditions gate it, and what user-visible or system-visible behavior results.

## 3. Separate evidence from interpretation

A discovery report must distinguish:

- **Evidence** — what the repository or runtime artifacts show;
- **Finding** — what that evidence means relative to the question being investigated;
- **Recommendation** — what should happen next.

Do not present assumptions as findings.

## 4. Prefer bounded conclusions

Do not broaden the discovery beyond the governing question merely because adjacent code is interesting.

If adjacent concerns are material, record them separately as risks, related observations, or follow-up candidates.

---

# Capability classification

When auditing whether a requested capability exists, classify each relevant capability using one of these states:

- **Exists** — implemented and materially satisfies the requirement being assessed.
- **Partial** — some supporting behavior exists, but the requirement is not fully satisfied.
- **Missing** — no meaningful implementation supporting the requirement was found.
- **Broken** — an implementation exists but evidence shows that it cannot currently satisfy its intended behavior.
- **Obsolete** — implementation exists but no longer matches the current architecture, product model, contract, or requirement.
- **Unknown** — available evidence is insufficient to classify responsibly.

Use `Unknown` rather than guessing.

---

# Workflow

## Phase 1 — Establish the discovery question

Before inspecting code, identify:

1. the governing issue, request, or engineering question;
2. the capability or behavior being evaluated;
3. the relevant acceptance boundary or desired system behavior;
4. known repositories or subsystems likely involved;
5. explicit out-of-scope areas, if any.

If the request is ambiguous enough that multiple materially different discoveries would result, stop and request clarification rather than selecting a scope silently.

---

## Phase 2 — Map the relevant system surface

Identify the code and system areas that can materially answer the discovery question.

This may include multiple repositories.

Create a lightweight inspection map before drawing conclusions. The map should identify the likely path through relevant layers, such as:

```text
User surface
  ↓
Frontend behavior
  ↓
API / application boundary
  ↓
Domain / service behavior
  ↓
Persistence / external integration
```

The exact shape is system-specific. Do not force layers that do not exist.

---

## Phase 3 — Inspect and trace

Inspect the relevant system artifacts and trace the actual behavior.

For each important conclusion, gather concrete evidence such as:

- repository and file path;
- symbol, route, component, model, or migration;
- current contract or data shape;
- test coverage or absence of coverage;
- execution relationship between relevant pieces.

Do not rely only on keyword search results. Open and understand the relevant implementation.

When multiple repositories participate in the behavior, trace the contract across repository boundaries instead of reviewing each repository in isolation.

---

## Phase 4 — Compare actual behavior to required behavior

For each relevant requirement or capability:

1. state the required or expected behavior;
2. state what currently exists;
3. classify it as Exists, Partial, Missing, Broken, Obsolete, or Unknown;
4. explain the gap, if any;
5. identify evidence supporting the conclusion.

Do not turn recommendations into implementation detail unless that detail is necessary to explain the finding.

---

## Phase 5 — Identify risks and dependencies

Record dependencies or risks that materially affect future implementation, such as:

- authorization boundaries;
- API contract coupling;
- schema or migration requirements;
- frontend/backend coordination;
- external service dependencies;
- operational constraints;
- data integrity concerns;
- duplicated or conflicting existing implementations;
- known architectural constraints.

This is not permission to redesign the system during discovery.

---

## Phase 6 — Produce the discovery report

The output must be usable directly in an engineering issue or planning discussion.

Use this structure unless the governing request requires another format:

```markdown
# Discovery Result

## Question Investigated
<What was investigated>

## Bottom Line
<Concise conclusion>

## Current System
<Evidence-backed description of the relevant implementation>

## Capability Assessment

| Capability / Requirement | Status | Evidence | Gap |
| --- | --- | --- | --- |
| ... | Exists / Partial / Missing / Broken / Obsolete / Unknown | ... | ... |

## Risks / Dependencies
<Only material items>

## Recommended Next Steps
<Implementation issues, design work, or further discovery that should follow>

## Out of Scope / Not Investigated
<Important boundaries or unknowns>
```

The report should be concise enough to use operationally but detailed enough that another engineer or agent can understand how the conclusions were reached.

---

# Follow-up issue discipline

Discovery may identify implementation work, but it must not perform that work.

When follow-up work is needed:

- group findings into coherent implementation issues;
- keep issue boundaries aligned to system responsibility rather than arbitrary file groupings;
- distinguish blocking prerequisites from later improvements;
- avoid creating duplicate issues for functionality already covered elsewhere;
- preserve important evidence from discovery in the follow-up issue so future implementation does not need to rediscover the same facts.

A discovery report may recommend issue titles and scopes, but issue creation is a separate action unless explicitly requested.

---

# Prohibited discovery behavior

During discovery, do not:

- modify source code;
- modify tests;
- change configuration;
- create migrations;
- refactor unrelated code;
- commit or push changes;
- open implementation pull requests;
- claim runtime behavior based only on naming or documentation;
- manufacture gaps merely to produce findings;
- expand scope without stating that expansion;
- treat recommendations as facts.

If a repository must be changed to answer the question, discovery is no longer purely investigative. Stop and obtain explicit authorization for the appropriate implementation workflow.

---

# Definition of discovery completion

A discovery follows this skill only when:

- the governing question is explicit;
- the relevant system surface was inspected rather than assumed;
- important behavior was traced through the layers that materially determine it;
- findings are supported by concrete repository or runtime evidence;
- capabilities are classified consistently;
- evidence, findings, and recommendations are distinguishable;
- unknowns are stated rather than guessed;
- relevant risks and dependencies are captured;
- no implementation changes were made;
- follow-up work is expressed as recommendations or implementation issues;
- the final report is usable by the next engineering phase without requiring the same discovery to be repeated.
