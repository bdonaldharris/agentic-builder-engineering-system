---
name: application-implementation-workflow
description: Govern the full application implementation lifecycle for AI-assisted software construction. Use when Agent #1 is implementing an application change and the workflow must enforce a real handoff to a separately invoked Agent #2 before commit or push, correction and re-review, integration PR creation, Promotion Protection, final-head PR review, human approval, and intentionally boring promotion.
---

# Application Implementation Workflow

## Purpose

This skill defines the canonical implementation lifecycle for application software constructed with AI agents.

It exists to make the engineering workflow procedural and repeatable rather than dependent on conversational memory, individual agent habits, or project-specific improvisation.

## Applicability

Use this workflow for all application builds unless the project explicitly declares a different governing workflow.

Static websites are excluded unless the project explicitly opts into this workflow.

The workflow is application-agnostic. Agent products are configurable per project; the roles are not.

- **Agent #1 — Implementation Agent**
- **Agent #2 — Independent Review Agent**

A project may assign Codex, Claude, or another capable agent to either role. The procedural responsibilities remain the same.

---

# Core invariant

Implementation must be independently reviewed and corrected before the reviewed artifact is committed and allowed to advance toward integration.

The integration PR is the final implementation-review boundary for the normal application path.

If a project explicitly permits a direct-to-production implementation path such as an emergency hotfix, that PR inherits the same final implementation-review responsibilities because it bypasses integration.

The promotion path must not become another implementation or feature-review cycle.

**Promotion expectation: zero gum on the bottom of the shoe.**

---

# Role-separation and handoff invariant

Agent #1 and Agent #2 are separate workflow participants.

**Agent #1 must not satisfy the Agent #2 gate by spawning, delegating to, simulating, impersonating, or internally instantiating its own reviewer.**

When Agent #1 reaches the mandatory review hold point, Agent #1 must stop and return control to the workflow coordinator, builder, or calling environment so that Agent #2 can be invoked separately.

The required independence is procedural, not merely conceptual. A sub-agent created and controlled by Agent #1 is still part of Agent #1's execution context for purposes of this workflow and does not constitute the required independent handoff.

A compliant handoff means:

1. Agent #1 leaves the implementation in the reviewable state required by this workflow;
2. Agent #1 reports what was implemented, validation performed, and repository state;
3. Agent #1 stops;
4. a separately invoked Agent #2 receives the artifact and performs `independent-implementation-review`;
5. only the resulting Agent #2 verdict may advance or return the artifact for correction.

The same separation applies to correction re-review. Agent #1 must not self-authorize advancement after applying corrections.

---

# Branch model

Every ordinary implementation issue begins from the project's designated integration branch, such as `staging`, `integration`, or another explicitly documented non-production integration branch.

Agent #1 must create or switch to an issue-specific feature/fix branch before committing implementation work.

Do not commit implementation directly to the integration branch.

The exact branch naming convention is repository-specific and should follow the repository's existing standards.

A project may explicitly document a direct-to-production exception path such as `hotfix/*`. Such a path is an implementation path, not a promotion path, and must preserve the review guarantees defined by this skill.

---

# Phase 1 — Agent #1 implementation

Agent #1 performs the implementation locally.

Agent #1 must:

1. inspect the issue and current repository state;
2. inspect relevant architecture and existing patterns before changing code;
3. create/use the appropriate issue branch from the current integration branch;
4. implement only the issue scope;
5. add or update appropriate tests and contracts;
6. run focused and relevant regression validation;
7. inspect the full working-tree diff;
8. report implementation details and repository state.

## Mandatory hold point

At the end of Phase 1, Agent #1 must **not**:

- commit;
- push;
- open a pull request;
- invoke or manufacture its own Agent #2 review;
- continue into the review phase under a different persona or sub-agent.

The implementation must remain locally reviewable.

Agent #1 must stop and hand control back for a separately invoked Agent #2 review.

---

# Phase 2 — Agent #2 independent review

Agent #2 is invoked separately from Agent #1 and independently reviews the actual implementation, not merely Agent #1's summary.

The review procedure must follow `independent-implementation-review`.

Agent #2 must inspect as appropriate:

- repository state;
- working-tree diff;
- architecture and layering;
- issue acceptance criteria;
- authorization/security boundaries;
- API/runtime contract alignment;
- tests and validation;
- regression risk;
- scope discipline;
- repository conventions.

Agent #2 must not modify the implementation during review.

## Review outcomes

Agent #2 returns one of:

- **Approved**
- **Changes Required**

Findings should be classified according to the project's review conventions, commonly Blocking, Non-blocking, or Informational.

Do not manufacture findings merely to produce a review.

---

# Phase 3 — Correction and re-review loop

If Agent #2 returns **Changes Required**:

1. control returns to Agent #1;
2. Agent #1 applies only the required corrections and any explicitly accepted non-blocking improvements;
3. Agent #1 re-runs appropriate validation;
4. Agent #1 still does **not** commit, push, or open a PR;
5. Agent #1 stops again and hands control back;
6. a separately invoked Agent #2 re-reviews the corrected local implementation.

Repeat this handoff loop until Agent #2 explicitly approves the implementation.

A correction pass does not grant permission to widen the issue scope or to self-approve the corrected artifact.

---

# Phase 4 — Commit, push, and integration PR

Only after a separately invoked Agent #2 approval may Agent #1:

1. verify the approved working-tree state has not changed;
2. stage only reviewed files;
3. commit the reviewed implementation;
4. push the issue branch;
5. open a pull request targeting the designated integration branch.

If a substantive code change becomes necessary after Agent #2 approval but before the commit/PR is created, stop. The changed artifact must return through the Agent #1 → separate Agent #2 review boundary before proceeding.

The PR must describe the reviewed implementation, validation, and issue scope accurately.

---

# Phase 5 — Mandatory Promotion Protection comment

Immediately after every implementation PR to the integration branch is created, Agent #1 must post the canonical Promotion Protection comment.

A commit/push/PR handoff that omits this comment is incomplete.

## Canonical Promotion Protection comment

```markdown
## Promotion Protection

This PR is the **final implementation-review boundary** for issue <ISSUE>.

Review this implementation as production-bound software, not only against the stated acceptance criteria.

Before this PR is merged to the integration branch, review the changed behavior and its relevant surrounding system for:

- correctness and edge cases;
- architecture and repository-pattern consistency;
- API, domain, and runtime contract consistency;
- authorization, security, and privacy;
- persistence and data-access behavior;
- performance, including N+1 queries and unnecessary database or network round trips;
- concurrency and idempotency where applicable;
- failure handling and operational behavior;
- regressions and adjacent-system effects;
- OpenAPI and documentation drift;
- missing or weak tests;
- duplicated functionality or unnecessary divergence from established patterns.

All blocking implementation findings must be resolved **here**, before merge to the integration branch. Substantive corrections must follow the normal correction and re-review workflow.

Do not knowingly carry corrective implementation work into promotion.

The artifact promoted to production should be the already-reviewed artifact from the integration branch.

A promotion PR is **not another feature-review cycle**. Its purpose is artifact and promotion-integrity verification only.

If a new issue is discovered after this PR reaches the integration branch:

- **Blocking:** fix it through the normal implementation/review workflow before promotion.
- **Non-blocking:** create a follow-up backlog issue and do not expand the promotion PR.

**Promotion expectation: zero gum on the bottom of the shoe.**
```

Projects may replace generic branch/environment wording with concrete names while preserving the protection boundary.

---

# Phase 6 — Integration PR review and merge gate

The integration PR is the last place where implementation findings are expected to be discovered and resolved.

Use `integration-pr-review` for this boundary.

A configured PR reviewer must review the implementation as production-bound software, not merely verify acceptance criteria.

If the repository has a configured automated PR reviewer, do not merge while that review is still in progress.

For repositories using Codex's current GitHub review behavior:

- a Codex 👍 reaction indicates the automated review completed without review suggestions;
- a Codex review/comment indicates the automated review completed with findings that must be evaluated before merge.

A substantive PR head change after completed automated review means that review no longer covers the merge candidate.

Do not re-trigger automated review after every individual correction commit. Finish the correction cycle, stabilize the PR head, then explicitly request one fresh automated review of the final head.

For Codex, request the fresh review with a new top-level PR comment consisting of:

```text
@codex review
```

A reply inside an existing review thread or a general mention is not a substitute.

After automated review completes and all blocking findings are resolved, obtain the project's required human approval.

Do not treat an automated reaction as the required human approval.

If substantive PR-review corrections are needed, they return through the same Agent #1 → separate Agent #2 correction/re-review discipline before the PR head is considered stable.

---

# Direct-to-production implementation exceptions

If a project explicitly permits an implementation PR to target production directly, that PR is not a promotion PR.

Because it bypasses the integration PR boundary, it must inherit the same protections:

- full-scope production-bound implementation review;
- completion of configured automated PR review on the stable final head;
- fresh automated review after substantive correction cycles;
- resolution of blocking findings before merge;
- the project's required human approval gate;
- separate Agent #2 review for substantive implementation corrections.

Urgency does not erase role separation or review independence.

---

# Phase 7 — Promotion

Promotion moves an already-reviewed integration artifact to production.

The promotion PR should be intentionally boring.

It must contain no new implementation, speculative cleanup, opportunistic refactoring, newly invented feature behavior, or known corrective work that should have been resolved in integration.

Promotion review is limited to artifact and promotion integrity.

This workflow does **not** include a mandatory second Agent #2 feature review of the promotion PR.

Do not invent an Agent #2 promotion-review phase unless a project explicitly adopts one for a separate reason.

---

# Blocking vs. follow-up findings

A finding blocks promotion when production would knowingly receive an incorrect, unsafe, contract-breaking, materially incomplete, or operationally unacceptable artifact.

Blocking findings return through the implementation/review workflow before promotion.

A legitimate improvement that does not invalidate the approved artifact should become a separate backlog issue rather than promotion work.

---

# Prompt-generation requirement

Any agent or coordinator generating workflow prompts from this skill must preserve the required hold points and handoff boundary.

In particular:

- implementation prompts must prohibit commit/push/PR before separate Agent #2 approval;
- Agent #1 prompts must explicitly stop at the review handoff and must not instruct Agent #1 to spawn or simulate Agent #2;
- Agent #2 review must be invoked separately;
- correction prompts must prohibit commit/push/PR before separate re-review;
- approval handoffs must not silently introduce unreviewed changes;
- integration-PR prompts must require completion of configured automated review before the human merge decision;
- integration-PR prompts must request one fresh automated review after the final substantive head change in a completed correction cycle, not after every correction commit;
- direct-to-production implementation prompts must preserve the same review protections;
- promotion prompts must not invent a new implementation-review phase.

Whenever generating the Agent #1 commit/push/PR prompt, the Promotion Protection section is mandatory.

---

# Workflow summary

```text
Issue
  ↓
Agent #1 implements locally
  ↓
NO COMMIT / NO PUSH / NO PR
  ↓
AGENT #1 STOPS
  ↓
External handoff / coordinator regains control
  ↓
Separately invoked Agent #2 independent review
  ├── Changes Required
  │      ↓
  │   hand back to Agent #1
  │      ↓
  │   Agent #1 corrections
  │      ↓
  │   AGENT #1 STOPS
  │      ↓
  │   separately invoked Agent #2 re-review
  │      └── repeat until Approved
  │
  └── Approved
         ↓
      hand back to Agent #1
         ↓
      commit / push / PR → integration
         ↓
      Promotion Protection
         ↓
      integration-pr-review
         ↓
      required human approval
         ↓
      integration
         ↓
      intentionally boring promotion
         ↓
      production
```

---

# Definition of workflow compliance

An implementation follows this skill only when:

- Agent #1 does not commit before independent review;
- Agent #1 stops at the review boundary rather than spawning, simulating, or controlling Agent #2;
- Agent #2 is separately invoked after the handoff;
- Agent #2 reviews the actual local implementation;
- blocking findings are corrected by Agent #1 and separately re-reviewed by Agent #2;
- only an approved artifact is committed and pushed;
- the integration PR receives Promotion Protection immediately after creation;
- configured automated PR review is allowed to complete;
- the stable final PR head receives fresh automated review after substantive changes;
- the required human approval gate is satisfied before integration merge;
- blocking findings are resolved before promotion;
- non-blocking late discoveries become backlog issues;
- promotion introduces no new implementation;
- no unrequested Agent #2 promotion-review phase is invented;
- the artifact reaches production with **zero gum on the bottom of the shoe**.
