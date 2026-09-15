# Integration PR Review

## Purpose

This skill defines the canonical review and merge-gating procedure for implementation pull requests targeting a project's designated integration branch.

It exists to ensure that the committed pull-request artifact receives a complete production-bound review before integration merge, that automated review covers the actual final PR head, and that blocking findings are resolved before promotion.

This skill is application-agnostic. Repository-specific branch names, CI systems, and automated reviewers may vary.

---

# Applicability

Use this skill for implementation pull requests targeting an integration branch such as:

- `staging`;
- `integration`;
- another explicitly designated non-production integration branch.

A direct-to-production implementation PR, such as an explicitly permitted emergency hotfix, should inherit the same implementation-review protections because it bypasses the normal integration boundary.

Do not use this skill for an ordinary promotion PR from integration to production. Promotion review is limited to artifact and promotion integrity and must not become another feature-review cycle.

---

# Core invariant

The integration PR is the final implementation-review boundary.

The merge candidate must be reviewed as production-bound software, and the completed review must cover the actual stable PR head that will be merged.

Do not knowingly merge blocking implementation defects into integration with the intention of fixing them during promotion.

**Promotion expectation: zero gum on the bottom of the shoe.**

---

# Relationship to other skills

This skill does not replace local independent implementation review.

The normal application path is:

1. Agent #1 implements locally under `application-implementation-workflow`.
2. Agent #2 performs `independent-implementation-review` before commit/push/PR.
3. After approval, Agent #1 commits, pushes, and opens the integration PR.
4. This skill governs review of the committed PR artifact and the merge decision.

Local Agent #2 review and integration PR review are separate engineering controls over different artifacts and lifecycle stages.

---

# Phase 1 — Establish the review target

Before reviewing findings or deciding merge eligibility, identify the actual PR artifact.

Confirm:

- repository;
- PR number;
- source branch;
- target integration branch;
- governing issue or change scope;
- current PR head commit SHA;
- current CI/check state;
- whether the required Promotion Protection comment is present when the governing workflow requires it.

The current PR head is the artifact under review.

Do not assume that an earlier automated or human review still covers the current head.

---

# Phase 2 — Full-scope production-bound review

Review the PR as software intended to reach production, not merely as a checklist against acceptance criteria.

Inspect, where applicable:

- correctness and edge cases;
- issue-scope completeness;
- architecture and layering;
- repository-pattern consistency;
- API, domain, persistence, and runtime contract consistency;
- authorization, security, and privacy boundaries;
- data-access behavior;
- database and network efficiency, including N+1 patterns and avoidable round trips;
- concurrency and idempotency;
- failure handling and operational behavior;
- migrations and compatibility;
- external integration behavior;
- OpenAPI, schema, and documentation drift;
- regression risk and adjacent-system effects;
- missing, weak, misleading, or implementation-coupled tests;
- duplicated functionality;
- unnecessary divergence from established system patterns;
- unrequested scope expansion.

Passing tests and satisfied acceptance criteria do not, by themselves, establish implementation quality.

---

# Phase 3 — Automated review completion gate

If the repository has a configured automated PR reviewer, do not make the final merge decision while that review is still in progress.

The automated review must complete against the current review candidate.

For repositories using Codex's current GitHub review behavior:

- a Codex 👍 reaction indicates that review completed without review suggestions;
- a Codex review or comment indicates that review completed with findings that must be evaluated before merge.

Do not treat a reaction as human approval.

Automated-review semantics may differ for other tools. Use the repository's documented reviewer behavior when available.

---

# Phase 4 — Evaluate findings

Every legitimate finding must be classified according to its effect on merge eligibility.

## Blocking

A finding is blocking when merging the PR would knowingly place an incorrect, unsafe, contract-breaking, materially incomplete, or operationally unacceptable artifact into integration.

Blocking findings must be resolved before merge.

Examples may include:

- incorrect runtime behavior;
- broken authorization or security boundaries;
- API or persistence contract mismatch;
- missing required behavior;
- material regression;
- unsafe migration behavior;
- severe performance defect;
- unhandled failure mode with material impact;
- tests that conceal a real defect rather than validate behavior.

## Non-blocking

A legitimate improvement is non-blocking when the current artifact remains correct and production-acceptable without it.

If it is outside the current issue boundary, create or recommend a follow-up backlog issue rather than expanding the implementation unnecessarily.

## Informational

Informational observations do not affect merge eligibility and require no corrective action unless explicitly accepted.

Do not manufacture findings merely to make the review appear rigorous.

---

# Phase 5 — Correction cycle

When blocking findings require substantive implementation changes:

1. return the change through the normal Agent #1 correction and Agent #2 independent re-review discipline;
2. apply corrections on the implementation branch;
3. run appropriate validation;
4. push the completed correction cycle to the PR;
5. allow CI and repository checks to settle;
6. establish the new stable PR head.

Do not trigger a fresh automated PR review after every individual correction commit while the correction cycle is still in progress.

Complete the intended corrections first.

---

# Phase 6 — Stable-final-head review

A substantive PR head change invalidates a completed automated review of the previous head for merge-gating purposes.

Once the correction cycle is complete and the PR head is stable, explicitly request one fresh automated review of that final head when the repository's reviewer requires an explicit retrigger.

For Codex, use a new top-level PR comment containing:

```text
@codex review
```

A reply inside an existing review thread, an edited prior comment, or a general Codex mention is not a substitute for the top-level review trigger.

Wait for that review to complete.

If the final-head review produces new blocking findings:

1. perform another correction cycle;
2. stabilize the PR head again;
3. request one new final-head review;
4. repeat until no blocking findings remain.

If another substantive change occurs after the final-head review completes, the review no longer covers the merge candidate and must be repeated.

---

# Phase 7 — Human approval gate

After automated review of the stable final head completes and all blocking findings are resolved, obtain the project's required human approval.

Where branch protection or repository rulesets are available, prefer technical enforcement of at least one qualifying approving review before merge.

An automated review, bot reaction, CI success, or Agent #2 local approval does not substitute for the required human approval when the project requires one.

---

# Phase 8 — Merge eligibility decision

The integration PR is eligible to merge only when all applicable conditions are true:

- the intended issue scope is implemented;
- the current PR head is the reviewed merge candidate;
- CI and required checks are acceptable;
- configured automated review has completed on the stable final head;
- all blocking findings are resolved;
- substantive corrections have completed Agent #2 re-review where required by the governing implementation workflow;
- the required human approval is present;
- no known implementation defect is being intentionally deferred to promotion;
- no unreviewed substantive change occurred after the final review.

Return one of these explicit outcomes:

- **APPROVED FOR INTEGRATION MERGE**
- **NOT APPROVED — CHANGES REQUIRED**
- **NOT APPROVED — REVIEW INCOMPLETE**

Do not imply merge readiness when a required review is still running, stale, or missing.

---

# Promotion Protection

This skill protects the boundary between implementation and promotion.

After integration merge, ordinary promotion should move the already-reviewed integration artifact toward production without new implementation.

Do not use a promotion PR to perform:

- corrective implementation;
- opportunistic refactoring;
- acceptance-criteria completion;
- newly discovered feature behavior;
- another full feature-review cycle.

If a blocking defect is discovered after integration merge, fix it through the normal implementation/review workflow before promotion.

If a newly discovered item is genuinely non-blocking, place it in the backlog rather than expanding promotion.

---

# Direct-to-production implementation exceptions

If a project explicitly permits an implementation PR to target production directly, that PR inherits this skill's protections because there is no later integration boundary to catch defects.

It must therefore receive:

- full-scope production-bound implementation review;
- configured automated review of the stable final head;
- a fresh final-head review after substantive correction cycles;
- resolution of blocking findings;
- required human approval.

Urgency does not erase the implementation-review boundary.

---

# What this skill does not do

This skill does not:

- implement corrections itself;
- replace Agent #2 local independent review;
- define repository-specific branch protection configuration;
- require redundant automated review after every correction commit;
- invent a mandatory Agent #2 review of the promotion PR;
- turn non-blocking discoveries into promotion work;
- treat a successful automated review as the required human approval.

---

# Workflow summary

```text
Integration PR opened
        ↓
Establish PR head / scope / checks
        ↓
Production-bound PR review
        ↓
Configured automated review completes
        ↓
Blocking findings?
   ├── Yes
   │     ↓
   │  Agent #1 correction cycle
   │     ↓
   │  Agent #2 re-review where required
   │     ↓
   │  push completed correction cycle
   │     ↓
   │  stabilize PR head
   │     ↓
   │  trigger one fresh automated review
   │     └── repeat if new blocking findings appear
   │
   └── No
          ↓
Stable-final-head automated review complete
          ↓
Required human approval
          ↓
APPROVED FOR INTEGRATION MERGE
          ↓
Integration
          ↓
Intentionally boring promotion
```

---

# Definition of compliance

An integration PR follows this skill only when:

- the merge candidate is explicitly identified;
- review considers production-bound behavior rather than acceptance criteria alone;
- configured automated review is allowed to complete;
- blocking findings are resolved before merge;
- correction cycles are completed before requesting another automated review;
- one fresh automated review covers the stable final head after substantive changes;
- any later substantive head change invalidates that review and triggers another final-head cycle;
- required human approval is obtained;
- no blocking corrective work is intentionally carried into promotion;
- promotion remains an artifact-integrity step rather than a second implementation cycle;
- the artifact reaches promotion with **zero gum on the bottom of the shoe**.
