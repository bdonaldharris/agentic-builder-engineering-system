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

Implementation must be fully reviewed and corrected before the artifact leaves the integration environment.

The promotion path must not become another implementation or feature-review cycle.

**Promotion expectation: zero gum on the bottom of the shoe.**

---

# Branch model

Every implementation issue begins from the project's designated integration branch.

Examples include:

- `staging`
- `integration`
- another explicitly documented non-production integration branch

Agent #1 must create or switch to an issue-specific feature/fix branch before committing implementation work.

Do not commit implementation directly to the integration branch.

The exact branch naming convention is repository-specific and should follow the repository's existing standards.

---

# Phase 1 — Agent #1 implementation

Agent #1 performs the implementation locally.

Agent #1 must:

1. inspect the issue and current repository state;
2. inspect the relevant architecture and existing patterns before changing code;
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
- open a pull request.

The implementation must remain locally reviewable until Agent #2 completes an independent review.

---

# Phase 2 — Agent #2 independent review

Agent #2 independently reviews the actual implementation, not merely Agent #1's summary.

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

Findings should be classified according to the project's review conventions, commonly:

- Blocking
- Non-blocking
- Informational

Do not manufacture findings merely to produce a review.

---

# Phase 3 — Correction and re-review loop

If Agent #2 returns **Changes Required**:

1. Agent #1 applies only the required corrections and any explicitly accepted non-blocking improvements.
2. Agent #1 re-runs appropriate validation.
3. Agent #1 still does **not** commit, push, or open a PR.
4. Agent #2 re-reviews the corrected local implementation.

Repeat this loop until Agent #2 explicitly approves the implementation.

A correction pass does not grant permission to widen the issue scope.

---

# Phase 4 — Commit, push, and integration PR

Only after Agent #2 approval may Agent #1:

1. verify the approved working-tree state has not changed;
2. stage only reviewed files;
3. commit the reviewed implementation;
4. push the issue branch;
5. open a pull request targeting the designated integration branch.

If a substantive code change becomes necessary after Agent #2 approval but before the commit/PR is created, stop. The changed artifact must return through review before proceeding.

The PR must describe the reviewed implementation, validation, and issue scope accurately.

---

# Phase 5 — Mandatory Promotion Protection comment

**Immediately after every implementation PR to the integration branch is created, Agent #1 must post the canonical Promotion Protection comment below.**

This step is mandatory.

A commit/push/PR handoff that omits this comment is incomplete.

The purpose is to establish the implementation PR as the final implementation/review boundary and prevent defects from being knowingly carried into the promotion PR.

## Canonical Promotion Protection comment

Post this as a top-level PR comment, replacing `<ISSUE>` with the governing issue reference when appropriate:

```markdown
## Promotion Protection

This PR is the implementation and review boundary for issue <ISSUE>.

Before this work is promoted from the integration branch to production:

- all implementation findings must be resolved here;
- all contract, test, architecture, security, and documentation findings must be resolved before this PR is merged to the integration branch;
- no known corrective work should be deferred into the promotion PR;
- the artifact promoted to production should be the already-reviewed artifact from the integration branch;
- the promotion PR must not contain new implementation changes or become another feature-review cycle.

If a new issue is discovered after this PR reaches the integration branch, determine whether it blocks promotion.

If it blocks promotion:
- fix it in the integration branch;
- re-run the normal implementation/review workflow;
- resolve it before promotion.

If it does not block promotion:
- create a follow-up backlog issue;
- do not expand the promotion PR.

**Promotion expectation: zero gum on the bottom of the shoe.**
```

Projects may replace the generic words `integration branch` and `production` with concrete branch names such as `staging` and `main`, but must preserve the intent and protection boundary.

---

# Phase 6 — Integration PR review

The integration PR is the last place where implementation findings are expected to be resolved.

If legitimate findings are discovered during PR review:

1. determine whether they block promotion;
2. if blocking, correct them on the issue branch/integration path;
3. repeat the normal Agent #1 correction and Agent #2 review discipline for substantive corrections;
4. update the integration PR;
5. resolve findings before merging to the integration branch.

Do not knowingly merge blocking implementation defects into the integration branch with the intention of fixing them during promotion.

If a discovered issue is genuinely non-blocking and not part of the current issue's acceptance boundary, create a follow-up backlog issue rather than expanding the promotion PR.

---

# Phase 7 — Promotion

Promotion moves an already-reviewed integration artifact to production.

The promotion PR should be intentionally boring.

It must contain:

- no new implementation;
- no speculative cleanup;
- no opportunistic refactoring;
- no newly invented feature behavior;
- no known corrective work that should have been resolved in integration.

The purpose of the promotion PR is to verify that the reviewed artifact leaving the integration branch is the artifact intended for production.

## No invented Agent #2 promotion-review step

This workflow does **not** include a mandatory second Agent #2 feature review of the promotion PR.

Do not invent an Agent #2 promotion-PR review phase unless a project explicitly adopts one for a separate reason.

The substantive independent review happens before the implementation is committed and again after substantive correction passes. Promotion integrity is not another implementation cycle.

---

# Blocking vs. follow-up findings

When a new issue is discovered after integration:

## Blocking

A finding blocks promotion when production would knowingly receive an incorrect, unsafe, contract-breaking, materially incomplete, or operationally unacceptable artifact.

Blocking findings return through the implementation/review workflow before promotion.

## Non-blocking

A legitimate improvement that does not invalidate the approved artifact should become a separate backlog issue.

Do not attach unrelated cleanup to the promotion PR merely because it was discovered late.

---

# Prompt-generation requirement

Any agent or coordinator generating workflow prompts from this skill must preserve the required hold points.

In particular:

> **Whenever generating the Agent #1 commit/push/PR prompt, the Promotion Protection section is mandatory. A prompt that omits it is incomplete.**

Likewise:

- implementation prompts must prohibit commit/push/PR before Agent #2 approval;
- correction prompts must prohibit commit/push/PR before re-review;
- approval handoffs must not silently introduce unreviewed changes;
- promotion prompts must not invent a new implementation-review phase.

---

# Workflow summary

```text
Issue
  ↓
Agent #1 implements locally
  ↓
NO COMMIT / NO PUSH / NO PR
  ↓
Agent #2 independent review
  ├── Changes Required
  │      ↓
  │   Agent #1 corrections
  │      ↓
  │   Agent #2 re-review
  │      └── repeat until Approved
  │
  └── Approved
         ↓
      Agent #1 commit
         ↓
      push issue branch
         ↓
      PR → integration branch
         ↓
      MANDATORY Promotion Protection comment
         ↓
      resolve all blocking findings in integration
         ↓
      merge to integration
         ↓
      intentionally boring promotion PR
         ↓
      production
```

---

# Definition of workflow compliance

An implementation follows this skill only when:

- Agent #1 does not commit before independent review;
- Agent #2 reviews the actual local implementation;
- blocking findings are corrected and re-reviewed;
- only an approved artifact is committed and pushed;
- the integration PR receives the Promotion Protection comment immediately after creation;
- blocking findings are resolved before promotion;
- non-blocking late discoveries become backlog issues;
- promotion introduces no new implementation;
- no unrequested Agent #2 promotion-review phase is invented;
- the artifact reaches production with **zero gum on the bottom of the shoe**.
