---
name: application-implementation-workflow
description: Govern the full application implementation lifecycle for AI-assisted software construction. Use when Agent #1 is implementing an application change and the workflow must enforce inspection of existing architecture before inventing new patterns, clarification of material ambiguity instead of guessing, a real handoff to a separately invoked Agent #2, correction/re-review, integration PR review, explicit risk classification and disposition, Promotion Protection, and intentionally boring production promotion.
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

Implementation must be independently reviewed before the artifact advances.

The workflow must distinguish:

- whether a slice is safe to integrate;
- whether the eventual release is safe to promote to production.

Those are related but not identical decisions.

The integration branch exists to assemble slices, enable integration/UAT, and support dependent development. Production promotion is the release-risk boundary.

The workflow must preserve correctness and risk control without allowing avoidable review churn to destroy convergence or delivery throughput.

---

# Inspect before inventing

Before creating a new abstraction, service, component, integration path, domain construct, storage pattern, workflow, or architectural convention, inspect the existing application for:

- the current owner of the responsibility;
- similar or adjacent behavior;
- reusable abstractions;
- established repository patterns;
- existing contracts and extension points.

Prefer extending the established owning system over creating parallel capability unless the issue explicitly requires a new boundary or the existing architecture cannot responsibly support the change.

Do not introduce something new merely because it is locally convenient.

---

# Ambiguity and clarification invariant

**Do not guess through material ambiguity. Ask before inventing.**

If the issue, instructions, design intent, acceptance criteria, architecture ownership, contract behavior, data semantics, authorization rules, integration behavior, scope, or user experience is ambiguous in a way that could materially change the implementation, Agent #1 must stop and ask focused clarification questions.

Continue clarifying until the uncertainty is resolved enough to proceed responsibly.

Do not silently choose among materially different interpretations merely to keep implementation moving.

Clarification is not required for every minor implementation detail. Agent #1 may resolve a detail without asking when:

- an established repository pattern provides a clear answer;
- the choice does not materially alter product behavior, architecture, contracts, data semantics, authorization, integration ownership, or issue scope;
- the choice is within the normal authority of the implementation role.

If issue wording conflicts with current repository behavior or architecture, surface the conflict instead of deciding silently which source wins.

If implementation uncovers a material ambiguity that was not visible earlier, stop at that point and ask. Do not use code changes as the mechanism for making an unresolved product or architecture decision.

---

# Role-separation and handoff invariant

Agent #1 and Agent #2 are separate workflow participants.

**Agent #1 must not satisfy the Agent #2 gate by spawning, delegating to, simulating, impersonating, or internally instantiating its own reviewer.**

When Agent #1 reaches the mandatory review hold point, Agent #1 must stop and return control so Agent #2 can be invoked separately.

A compliant handoff means:

1. Agent #1 leaves the implementation in a reviewable state;
2. Agent #1 reports implementation, validation, and repository state;
3. Agent #1 stops;
4. a separately invoked Agent #2 performs `independent-implementation-review`;
5. only the resulting review and subsequent workflow-owner disposition determine advancement.

The same separation applies to correction re-review.

---

# Branch model

Every ordinary implementation issue begins from the project's designated integration branch, such as `staging`, `integration`, or another explicitly documented non-production integration branch.

Agent #1 must create or switch to an issue-specific feature/fix branch before committing implementation work.

Do not commit implementation directly to the integration branch.

A project may explicitly document a direct-to-production exception path such as `hotfix/*`. Such a path is an implementation path, not a promotion path, and must preserve the same independent-review protections while evaluating risk against the production boundary directly.

---

# Phase 1 — Agent #1 implementation

Agent #1 performs the implementation locally.

Agent #1 must:

1. inspect the issue and current repository state;
2. inspect the relevant architecture, ownership boundaries, reusable capabilities, and existing patterns before changing code;
3. clarify material ambiguity before implementation rather than guessing or inferring consequential behavior;
4. create/use the appropriate issue branch from the current integration branch;
5. implement only the issue scope;
6. prefer extension of established owning abstractions over parallel implementations unless a new boundary is justified;
7. add or update appropriate tests and contracts;
8. run focused and relevant regression validation;
9. inspect the full working-tree diff;
10. report implementation details and repository state.

## Mandatory hold point

At the end of Phase 1, Agent #1 must **not**:

- commit;
- push;
- open a pull request;
- invoke or manufacture its own Agent #2 review;
- continue into review under another persona or sub-agent.

Agent #1 must stop and hand control back for a separately invoked Agent #2 review.

---

# Phase 2 — Agent #2 independent review

Agent #2 is invoked separately and reviews the actual implementation artifact under `independent-implementation-review`.

That skill is the authoritative source for:

- complete-artifact approval;
- correction re-review convergence;
- Integration Blocker / Production Blocker / Follow-up / Informational classifications;
- finding quality;
- separation of classification from disposition;
- reviewer independence.

Do not reduce Agent #2 correction review to checking only the latest correction diff. Corrections are verified first, then Agent #2 re-establishes approval over the complete current artifact.

Do not instruct Agent #2 with language such as:

- `This is a correction review, not a new broad audit.`
- `Do not restart the overall implementation review.`

Those phrases can accidentally shrink the approval boundary.

---

# Phase 3 — Finding disposition and correction loop

Review findings surface risk; they do not automatically dictate implementation.

Canonical finding classifications are:

- **Integration Blocker**
- **Production Blocker**
- **Follow-up**
- **Informational**

Canonical dispositions are:

- **Fix before current merge**
- **Defer to backlog / follow-up issue**
- **No action**

The reviewer provides evidence, classification, and a recommended disposition. The workflow owner evaluates project context and decides final disposition.

If one or more findings are dispositioned `Fix before current merge`:

1. control returns to Agent #1;
2. Agent #1 applies the accepted corrections;
3. Agent #1 validates;
4. Agent #1 still does not commit/push/open a PR when the artifact is still in the pre-PR phase;
5. Agent #1 stops;
6. a separately invoked Agent #2 verifies the corrections first and then re-establishes approval over the complete current artifact;
7. repeat only while unresolved Integration Blockers remain for the current advancement boundary.

Do not force another correction cycle merely because a Production Blocker or Follow-up exists when its current disposition is deferred and the residual integration risk is acceptable.

If a requested correction itself exposes a material ambiguity, Agent #1 must stop and clarify before choosing a corrective design.

---

# Phase 4 — Commit, push, and integration PR

Only after the pre-PR artifact is approved for integration advancement may Agent #1:

1. verify the reviewed working-tree state has not changed;
2. stage only reviewed files;
3. commit;
4. push the issue branch;
5. open a pull request targeting the designated integration branch.

If substantive code changes occur after approval and before commit/PR creation, the changed artifact returns through separate Agent #2 review.

The PR must describe the reviewed implementation, validation, issue scope, and any known deferred findings that materially affect later production risk evaluation.

---

# Phase 5 — Mandatory Promotion Protection comment

Immediately after every implementation PR to the integration branch is created, Agent #1 must post the canonical Promotion Protection comment.

A commit/push/PR handoff that omits this comment is incomplete.

## Canonical Promotion Protection comment

```markdown
## Promotion Protection

This PR is the **implementation-review boundary for this slice before merge to the integration branch**.

Review the complete current artifact and relevant surrounding execution paths. Integration readiness and production readiness are related but are not the same gate.

Classify findings as:

- **Integration Blocker** — must be resolved before integration because the finding would materially prevent safe integration, meaningful UAT, or continued dependent development.
- **Production Blocker** — real defect/risk that may be acceptable in integration/UAT but must be risk-evaluated before production promotion.
- **Follow-up** — legitimate improvement or bounded edge case that can be placed in backlog.
- **Informational** — context only; no required corrective action.

For each finding, recommend one disposition:

- Fix before current merge
- Defer to backlog / follow-up issue
- No action

A finding is not automatically a merge blocker merely because it exists. Final disposition is determined by risk evaluation.

Integration Blockers must be resolved before merge to the integration branch. Known production-hardening findings may remain documented while the broader epic is assembled when integration risk is acceptable.

Before production promotion, all known relevant findings must be risk-evaluated. Only findings whose residual production risk is unacceptable require correction before release.

Promotion should remain intentionally boring: no planned feature implementation, opportunistic refactoring, acceptance-criteria completion, or hidden corrective feature work inside the promotion PR.

**Zero gum on the bottom of the shoe** means no knowingly unacceptable production risk, no planned corrective feature work hidden inside promotion, and no unresolved finding whose final disposition requires remediation before release.
```

Projects may substitute concrete branch/environment names while preserving the risk distinction.

---

# Phase 6 — Integration PR review and merge gate

Use `integration-pr-review` for this boundary.

That skill is the authoritative source for:

- complete current-artifact review;
- integration-versus-production risk classification;
- finding disposition;
- correction-cycle convergence;
- stable-final-head automated review;
- the canonical Codex final-head review request;
- human approval and integration merge eligibility;
- production-promotion risk evaluation guidance.

Do not reconstruct or paraphrase the canonical Codex review request in per-implementation prompts.

If PR-review corrections are accepted for the current merge, they return through Agent #1 and separate Agent #2 re-review. Agent #2 verifies the fixes first and then re-establishes approval over the complete current artifact.

After those approved PR corrections are committed and pushed, the correction handoff is **not complete at `PR updated`**. Finalization must continue under `integration-pr-review` through the required stable-final-head automated review request before the PR is treated as ready for integration review or merge.

---

# Direct-to-production implementation exceptions

A direct-to-production implementation PR bypasses the integration buffer.

Therefore, findings must be evaluated against the production release boundary directly.

Any finding whose residual production risk is unacceptable must be corrected before merge.

Use the same role separation, complete-artifact review, classification/disposition, automated-review, and human-approval protections.

---

# Phase 7 — Promotion

Promotion moves an integrated artifact toward production.

Promotion should be intentionally boring:

- no planned feature implementation;
- no opportunistic refactoring;
- no acceptance-criteria completion;
- no intentional feature-development cycle inside promotion.

`Boring` does not mean new findings cannot surface.

Promotion review is a **risk-evaluation gate**, not an automatic remediation gate.

A promotion finding may result in:

- correction before promotion;
- follow-up issue and promotion proceeds;
- no action.

If a finding is judged to be a true Production Blocker whose residual risk is unacceptable:

1. create a correction branch from the integration branch;
2. implement/review normally;
3. merge the correction back to integration;
4. refresh the promotion artifact;
5. re-run required promotion-integrity review.

Do not perform corrective feature work directly inside the promotion PR.

If residual production risk is acceptable, document it, create/reference follow-up work when appropriate, and promotion may proceed.

---

# Zero gum on the bottom of the shoe

Keep the phrase, but apply it to release risk.

It means:

- no knowingly unacceptable production risk;
- no planned corrective feature work hidden inside promotion;
- no unresolved finding whose final disposition explicitly requires remediation before release.

It does **not** mean every known edge case, hardening opportunity, Production Blocker under accepted residual risk, or Follow-up must be fixed before production.

---

# Prompt-generation requirement

Any agent or coordinator generating workflow prompts from this skill must preserve the workflow boundaries without restating the entire workflow.

In particular:

- implementation prompts should invoke this skill instead of reproducing it;
- Agent #1 must inspect current architecture and existing patterns before inventing new structures;
- Agent #1 must ask focused questions when material ambiguity remains instead of guessing through it;
- prompts should not manufacture questions for minor details already resolved by established repository patterns;
- Agent #1 must stop for a separately invoked Agent #2;
- correction prompts must preserve complete-artifact re-approval;
- correction-review prompts must not use language that narrows Agent #2 to only the latest diff;
- integration-PR prompts must invoke `integration-pr-review` rather than reconstruct its review contract;
- Agent #1 correction-finalization prompts must not end at commit/push/PR update when a substantive PR correction changed the head; they must preserve the required final-head automated review trigger defined by `integration-pr-review`;
- promotion prompts must preserve boring promotion while treating findings as risk inputs rather than automatic remediation commands.

The goal is to reduce prompt duplication, not move the workflow text into every prompt.

---

# Workflow summary

```text
Issue
  ↓
Agent #1 inspects existing architecture / ownership / patterns
  ↓
Material ambiguity?
  ├── Yes → ask focused questions → resolve before consequential implementation
  └── No
         ↓
      Agent #1 implements
         ↓
      Agent #1 stops
         ↓
      separate Agent #2 complete-artifact review
         ↓
      workflow owner evaluates finding classifications + dispositions
         ↓
      Integration Blocker requiring fix?
         ├── Yes → Agent #1 correction → separate Agent #2 re-review
         │                              ↓
         │                    verify fixes + re-establish whole-artifact approval
         │                              └── repeat only while Integration Blockers remain
         │
         └── No
                ↓
             commit / push / PR → integration
                ↓
             Promotion Protection
                ↓
             integration-pr-review
                ↓
             human approval
                ↓
             integration / UAT / dependent development
                ↓
             production-promotion risk evaluation
                ↓
             unacceptable residual production risk?
                ├── Yes → correction branch from integration → normal workflow
                └── No → intentionally boring promotion
```

---

# Definition of workflow compliance

An implementation follows this skill only when:

- Agent #1 inspects the current application architecture and established patterns before creating new abstractions or parallel capability;
- material ambiguity is clarified rather than guessed through;
- minor implementation details may follow established patterns without unnecessary questions;
- Agent #1 stops for separate Agent #2 review;
- Agent #2 approval applies to the complete current artifact;
- correction re-review verifies fixes and then re-establishes complete-artifact approval;
- findings use Integration Blocker, Production Blocker, Follow-up, or Informational classifications;
- classification and disposition remain separate;
- final disposition is risk-evaluated rather than automatically dictated by reviewers;
- integration is blocked only by unresolved Integration Blockers or incomplete required review;
- known production-hardening items may remain when documented and appropriately dispositioned;
- integration PR review follows `integration-pr-review`;
- a substantive PR correction is not considered finalized merely because the corrected commit was pushed; the required stable-final-head automated review request must be posted before integration readiness is declared;
- promotion remains intentionally boring without becoming an automatic remediation gate;
- corrective feature work is not performed inside promotion;
- production receives no knowingly unacceptable residual risk;
- **zero gum on the bottom of the shoe** is applied to production risk rather than interpreted as zero known follow-up work.
