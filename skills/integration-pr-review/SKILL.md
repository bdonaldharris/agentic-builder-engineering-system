---
name: integration-pr-review
description: Review and gate an implementation pull request at the integration boundary. Use for staging or integration PRs to review the full current artifact, distinguish Integration Blockers from Production Blockers and Follow-ups, separate finding classification from disposition, enforce stable-final-head automated review, and drive convergence without turning every finding into mandatory immediate work.
---

# Integration PR Review

## Purpose

This skill defines the canonical review and merge-gating procedure for implementation pull requests targeting a project's designated integration branch.

It exists to ensure that the committed pull-request artifact receives a complete review before integration merge, that automated review covers the actual stable PR head, that integration risk is distinguished from production risk, and that review cycles converge without turning every discovered edge case into immediate corrective work.

This skill is application-agnostic. Repository-specific branch names, CI systems, and automated reviewers may vary.

---

# Applicability

Use this skill for implementation pull requests targeting an integration branch such as `staging`, `integration`, or another explicitly designated non-production integration branch.

A direct-to-production implementation PR, such as an explicitly permitted emergency hotfix, inherits the production-risk protections in this skill because it bypasses the normal integration boundary.

Do not treat integration readiness and production readiness as the same decision.

---

# Core invariant

The integration PR is the final implementation-review boundary **for the slice entering integration**.

The integration branch exists to assemble slices, enable integration testing/UAT, and support continued dependent development. Therefore, a finding blocks integration only when it makes that activity unsafe or materially invalid.

Review should still surface production-bound risks, but the existence of a real defect does not automatically make it an Integration Blocker.

The workflow must balance:

- correctness;
- risk control;
- convergence;
- delivery throughput.

Do not design the process around endless correction loops where every possible edge case becomes mandatory immediate work.

---

# Relationship to other skills

The normal application path is:

1. Agent #1 implements under `application-implementation-workflow`.
2. Agent #2 performs `independent-implementation-review` before commit/push/PR.
3. After approval, Agent #1 commits, pushes, and opens the integration PR.
4. This skill governs review of the committed PR artifact and the integration merge decision.

When integration-PR review leads to implementation corrections, the subsequent Agent #2 correction re-review must verify those corrections first and then re-establish approval over the complete current artifact as defined by `independent-implementation-review`.

---

# Phase 1 — Establish the review target

Confirm:

- repository;
- PR number;
- source branch;
- target integration branch;
- governing issue/change scope;
- current PR head commit SHA;
- current CI/check state;
- required Promotion Protection comment.

The current PR head is the artifact under review.

Do not assume an earlier automated or human review still covers a changed head.

---

# Phase 2 — Complete current-artifact review

Review the PR as software that is intended eventually to reach production while remembering that the immediate gate is **integration readiness**, not production release.

Inspect the complete current artifact and relevant surrounding execution paths for, where applicable:

- correctness and edge cases;
- issue-scope completeness;
- architecture and repository-pattern consistency;
- API, domain, persistence, and runtime contracts;
- authorization, security, and privacy;
- data integrity and data-access/network efficiency;
- concurrency and idempotency;
- failure handling and operational behavior;
- migrations and compatibility;
- external integrations;
- OpenAPI/schema/documentation drift;
- regression risk and adjacent-system effects;
- missing, weak, or misleading tests;
- duplicated functionality or unnecessary divergence;
- unrequested scope expansion.

Passing tests and satisfied acceptance criteria are useful evidence but are not sufficient by themselves.

Complete the review of the current artifact before returning findings when reasonably possible. Consolidate presently identifiable Integration Blockers rather than intentionally drip-feeding them across successive PR heads.

Do not expand into unrelated repository archaeology merely to find more things to report.

---

# Phase 3 — Automated review completion gate

If the repository has a configured automated PR reviewer, do not make the final merge decision while the required review of the current candidate is still in progress.

For repositories using Codex's current GitHub review behavior:

- a Codex 👍 reaction indicates the automated review completed without review suggestions;
- a Codex review/comment indicates review completed with findings that must be evaluated.

Automated review surfaces information. It does not determine final disposition by itself and does not substitute for required human approval.

---

# Phase 4 — Canonical finding classification

Every legitimate finding must be classified by risk.

## Integration Blocker

A finding that must be resolved before merge to the integration branch because it would materially prevent safe integration, meaningful UAT, or continued dependent development.

Examples include:

- core acceptance behavior is broken;
- material authorization/privacy boundary failure in a normal or reasonably likely path;
- data corruption/loss;
- incompatible API/runtime contract;
- feature cannot be exercised reliably in staging;
- regression breaks another integrated capability;
- downstream epic work would build on an invalid foundation.

Integration Blockers must be resolved before integration merge unless new evidence causes the workflow owner to reclassify the risk.

## Production Blocker

A real defect or risk that may be acceptable in integration/UAT but must be risk-evaluated before production promotion.

A Production Blocker does **not automatically** require immediate correction while a broader epic is still being assembled.

It must be documented and tracked so the production promotion decision explicitly considers it.

## Follow-up

A legitimate improvement, bounded edge case, hardening opportunity, or non-critical defect that does not prevent safe integration or production release.

Follow-ups should normally become backlog work rather than expanding the current implementation.

## Informational

Context only. No required corrective action.

---

# Classification is not disposition

A finding does not dictate implementation automatically.

Canonical dispositions are:

- **Fix before current merge**
- **Defer to backlog / follow-up issue**
- **No action**

Reviewers surface evidence, classification, and a recommended disposition. The workflow owner evaluates project context and decides final disposition.

A finding may therefore be a Production Blocker while the disposition for the current integration PR is `Defer to backlog / follow-up issue`, provided its staging risk is acceptable and it remains tracked for production evaluation.

If a finding has already been adjudicated as Production Blocker, Follow-up, or Informational, later reviewers should not automatically relitigate it as an Integration Blocker. Reclassification requires concrete new evidence of a materially different failure mechanism, likelihood, or impact.

---

# Phase 5 — Correction cycle

When one or more findings are dispositioned **Fix before current merge**:

1. return the change through the normal Agent #1 correction and separate Agent #2 re-review discipline;
2. Agent #1 applies the accepted corrections;
3. Agent #1 validates and stops;
4. separately invoked Agent #2 verifies the corrections first;
5. Agent #2 then re-establishes approval over the complete current artifact using the convergence rules in `independent-implementation-review`;
6. all presently identifiable Integration Blockers are consolidated in that review;
7. repeat only while unresolved Integration Blockers remain;
8. push the completed correction cycle;
9. allow CI/checks to settle;
10. establish the new stable PR head.

Do not trigger a new automated PR review after every individual correction commit. Complete the correction/convergence cycle first.

Production Blockers and Follow-ups with an accepted deferred disposition do not force another correction cycle merely because they exist.

---

# Phase 6 — Stable-final-head review

A substantive PR head change invalidates completed automated review of the previous head for merge-gating purposes.

Once the correction cycle is complete, Agent #2 has re-established approval over the complete current artifact, and the PR head is stable, request one fresh automated review when the configured reviewer requires a retrigger.

For Codex, use a **new top-level PR comment using the Canonical Final-Head Codex Review Request below**.

`@codex review` by itself is only a trigger and is not the complete review request required by this workflow.

## Canonical Final-Head Codex Review Request

Replace `<INTEGRATION_BRANCH>` with the repository's designated integration branch.

```markdown
@codex review

Perform the **complete production-bound review** of this PR against the current stable head.

The immediate decision is whether this artifact is safe and valid to merge to `<INTEGRATION_BRANCH>`. Integration readiness and production readiness are not the same gate.

Review the **full current artifact** and relevant surrounding execution paths for:

- correctness and edge cases;
- architecture and repository-pattern consistency;
- API, domain, persistence, and runtime contract consistency;
- authorization, security, and privacy;
- data integrity and data-access/network efficiency;
- concurrency and idempotency where applicable;
- failure handling and operational behavior;
- regression risk and adjacent-system effects;
- OpenAPI, schema, and documentation drift;
- missing, weak, or misleading tests;
- duplicated functionality or unnecessary divergence;
- unrequested scope expansion.

Complete the review of the full current artifact before posting findings when reasonably possible. **Consolidate all presently identifiable findings into this review pass and do not intentionally drip-feed findings across successive PR heads.**

Classify each finding as:

**Integration Blocker** — must be resolved before merge to `<INTEGRATION_BRANCH>` because it would materially prevent safe integration, meaningful UAT, or continued dependent development.

**Production Blocker** — a real defect or risk that may be acceptable in integration/UAT but must be risk-evaluated before production promotion. It is not automatically a blocker for this integration merge.

**Follow-up** — legitimate improvement or bounded edge case that does not prevent integration or production and can be placed in backlog.

**Informational** — context only; no required corrective action.

For each finding, recommend one disposition:

- Fix before current merge
- Defer to backlog / follow-up issue
- No action

A finding is **not automatically a merge blocker merely because it exists**. Final disposition is determined by risk evaluation.

Do not relitigate an already-adjudicated Production Blocker, Follow-up, or Informational finding as an Integration Blocker unless concrete new evidence shows a materially different failure mechanism, likelihood, or impact.

Do not review only against acceptance criteria, and do not treat passing tests as sufficient evidence of implementation quality.

Promotion Protection applies: Integration Blockers must be resolved before integration merge. Known production-hardening findings may remain documented while the broader epic is under integration, but they must be risk-evaluated before production promotion.
```

The canonical template is the authoritative Codex final-head instruction. Do not recreate or paraphrase it in per-PR prompts except for repository-specific substitutions.

Wait for the review to complete.

If the review identifies new Integration Blockers, perform another correction/convergence cycle and then request one new final-head review of the changed head.

If it identifies only Production Blockers, Follow-ups, or Informational findings, evaluate and record their dispositions; do not automatically create another implementation cycle.

Do not post a second final-head request while a Codex review of the same head is still running.

No workflow can guarantee exhaustive defect discovery in one pass. The consolidation rule prevents process-induced narrowness; it does not claim mathematical completeness.

---

# Phase 7 — Human approval and integration merge eligibility

The integration PR is eligible to merge when all applicable conditions are true:

- intended issue scope is implemented sufficiently for integration;
- current PR head is the reviewed merge candidate;
- CI and required checks are acceptable;
- required automated review completed on the stable final head;
- no unresolved Integration Blocker remains;
- substantive corrections received separate Agent #2 re-review over the complete current artifact;
- Production Blockers/Follow-ups have documented dispositions where applicable;
- required human approval is present;
- no unreviewed substantive change occurred after the final review.

Return one of:

- **APPROVED FOR INTEGRATION MERGE**
- **NOT APPROVED — CHANGES REQUIRED**
- **NOT APPROVED — REVIEW INCOMPLETE**

Do not withhold integration merely because a real but integration-acceptable production-hardening item exists.

---

# Promotion Protection

Promotion Protection separates integration assembly from production release risk.

It means:

- Integration Blockers are resolved before merge to integration;
- known Production Blockers and Follow-ups may be carried in documented backlog while the broader epic is assembled when integration risk is acceptable;
- before production promotion, all known relevant findings are risk-evaluated;
- only findings whose residual production risk is unacceptable must be corrected before release.

Promotion Protection does **not** mean every known edge case, hardening opportunity, or follow-up must be fixed before integration or production.

---

# Promotion PR review — risk-evaluation gate

Promotion should remain intentionally boring:

- no planned feature implementation;
- no opportunistic refactoring;
- no acceptance-criteria completion;
- no intentional feature-development cycle inside promotion.

`Boring` does not mean reviewers are forbidden from discovering new findings.

If a finding surfaces during promotion review, evaluate its production risk and choose a disposition:

- **Correction before promotion**
- **Follow-up issue and promotion proceeds**
- **No action**

If a finding is judged to be a true Production Blocker whose residual risk is unacceptable:

1. create a correction branch from the integration branch;
2. implement and review the correction through the normal application workflow;
3. merge the correction back to integration;
4. refresh the promotion artifact;
5. re-run any promotion-integrity review required by the project.

Do not perform the corrective feature work directly inside the promotion PR.

If the residual production risk is acceptable, document the finding, create/reference follow-up work when appropriate, and promotion may proceed.

---

# Zero gum on the bottom of the shoe

Keep the principle, but apply it to production risk.

**Zero gum on the bottom of the shoe** means:

- no knowingly unacceptable production risk;
- no planned corrective feature work hidden inside promotion;
- no unresolved finding whose final disposition explicitly requires remediation before release.

It does **not** mean every known edge case, Production Blocker under accepted residual risk, or Follow-up must be fixed before release.

---

# Direct-to-production implementation exceptions

A direct-to-production implementation path has no integration buffer, so evaluate findings against the production release boundary directly.

Any finding whose residual production risk is unacceptable must be corrected before merge.

The same complete-artifact review, classification, disposition, automated-review, and human-approval protections apply.

---

# What this skill does not do

This skill does not:

- implement corrections itself;
- replace Agent #2 local independent review;
- define repository-specific branch protection;
- require automated review after every correction commit;
- claim reviewers can guarantee exhaustive one-pass defect discovery;
- make every discovered defect an Integration Blocker;
- make every Production Blocker an automatic immediate correction;
- allow production corrective work to be hidden inside promotion;
- treat automated review as the final risk owner.

---

# Workflow summary

```text
Integration PR
    ↓
review complete current artifact
    ↓
classify findings by risk
    ↓
workflow owner decides disposition
    ↓
Integration Blocker requiring fix?
 ├── Yes → Agent #1 correction
 │           ↓
 │        separate Agent #2 re-review
 │           ↓
 │        verify fixes + re-establish whole-artifact approval
 │           ↓
 │        push stable head
 │           ↓
 │        canonical final-head automated review
 │           └── repeat only if unresolved Integration Blockers remain
 │
 └── No → document Production Blockers / Follow-ups as needed
             ↓
          human approval
             ↓
          integration merge
             ↓
          UAT / dependent development / epic assembly
             ↓
          promotion risk evaluation
             ↓
          unacceptable production risk?
             ├── Yes → correction branch from integration → normal workflow
             └── No → promotion may proceed
```

---

# Definition of compliance

An integration PR follows this skill only when:

- the complete current artifact is reviewed;
- correction re-review verifies fixes and then re-establishes approval over the complete artifact;
- findings use Integration Blocker, Production Blocker, Follow-up, or Informational classifications;
- finding classification is distinct from disposition;
- final disposition is risk-evaluated by the workflow owner;
- presently identifiable Integration Blockers are consolidated rather than intentionally drip-fed;
- prior adjudications are not relitigated without new evidence;
- stable-final-head automated review uses the canonical request when Codex is configured;
- no unresolved Integration Blocker remains at integration merge;
- Production Blockers/Follow-ups may remain when appropriately documented and dispositioned;
- promotion is a risk-evaluation gate, not an automatic remediation gate;
- corrective implementation is not performed inside promotion;
- the release reaches production with **zero gum on the bottom of the shoe** as defined by acceptable residual production risk.
