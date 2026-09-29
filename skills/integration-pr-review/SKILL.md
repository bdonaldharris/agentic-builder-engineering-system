---
name: integration-pr-review
description: Shorthand: pr-review. Review and gate an implementation pull request using the narrow three-question contract: original acceptance criteria met, no new bug/regression introduced, and changed behavior works. Run one normal automated review on the stable initial head, independently evaluate findings, correct validated current-PR defects at their coherent issue-scoped behavior boundary, require complete-artifact Agent #2 re-review, and run one bounded final-head artifact-integrity review after each validated correction changes the pushed PR head.
---

# Integration PR Review

## Purpose

This skill governs review and merge gating for implementation pull requests targeting the designated integration branch.

Its job is not to perform a general application audit.

Its job is to answer whether the current PR should merge.

## Invocation model

The pull request and its governing issue are the authoritative sources of PR-specific implementation context, acceptance criteria, and changed artifact state.

This skill owns the reusable integration-PR review procedure. The invocation prompt does not need to restate the review contract or workflow rules already defined here.

When the agent is already operating in the correct repository workspace, a minimal invocation is sufficient, for example:

```text
review PR #1501 using pr-review
```

Resolve `pr-review` to this canonical skill, retrieve/read the PR and governing issue, and execute this workflow against the current PR artifact.

The governing rule is:

```text
Acceptance criteria met? Yes
New bug/regression introduced? No
Does it work? Yes

= MERGE
```

---

# Governing review contract

Every integration PR review answers only three questions:

1. **Acceptance criteria met?**
2. **New bug/regression introduced?**
3. **Does it work?**

A current PR requires correction only when review demonstrates:

- an acceptance criterion was not met;
- this PR introduced or materially worsened a bug/regression;
- the changed behavior does not work as intended.

Reviewers may inspect enough surrounding code and execution paths to determine those three things.

Do not turn integration review into a general audit of the application.

Diagnostic scenarios, reviewer-generated matrices, automated examples, and hardening ideas do **not** create new acceptance criteria.

A finding becomes blocking only when it demonstrates:

1. an **original issue acceptance criterion** is unmet;
2. the current artifact introduced or materially worsened a bug/regression; or
3. the implemented behavior does not work.

---

# What does not block this PR

Unless directly caused or materially worsened by the current PR, the following are suggestions:

- preexisting bugs or architectural weaknesses;
- historical-data/deployment cleanup;
- future hardening;
- adjacent improvements;
- broader contract completeness unrelated to the changed behavior;
- unrelated test gaps;
- refactoring or consistency opportunities;
- performance concerns outside the changed execution path;
- speculative concurrency concerns not introduced by the change;
- unrelated security observations;
- findings discovered only because the reviewer happened to trace nearby code.

Do not classify these as Integration Blockers or Production Blockers for the current PR.

Do not hold the PR open for them.

Route worthwhile observations to the repository's persistent `Application Improvement Suggestions` issue.

---

# Consolidated suggestion backlog

Each application repository should maintain one persistent backlog issue named:

`Application Improvement Suggestions`

Do not create a standalone GitHub issue for every review observation.

A suggestion entry should contain only:

- originating issue/PR;
- short description;
- why it may be worth considering;
- review-comment reference when available.

Adding a suggestion does not imply severity, priority, production gating, implementation commitment, or a future standalone issue.

A suggestion becomes planned work only when deliberately promoted later.

---

# Relationship to other skills

The normal flow is:

1. Agent #1 implements under `application-implementation-workflow`.
2. Separately invoked Agent #2 performs `independent-implementation-review`.
3. If `Yes -> No -> Yes`, Agent #1 commits, pushes, and opens the integration PR.
4. This skill governs the PR review and merge decision.

Agent #2 remains independent from Agent #1.

Automated-review findings are information, not commands, and must be independently evaluated against the same three-question contract.

---

# Phase 1 — Establish the PR artifact

Confirm:

- repository;
- PR number;
- governing issue;
- source and target branches;
- current PR head SHA;
- CI/check state;
- that the PR represents the implementation Agent #2 approved.

If the pushed artifact differs materially from the artifact Agent #2 approved, stop and resolve that mismatch before merge.

---

# Phase 2 — One normal automated review

Run the repository's normal automated/Codex PR review on the stable implementation head.

For Codex, use a top-level review request. A bare trigger is acceptable if repository automation needs only the trigger; if a canonical repository-specific request exists, use it.

Do not use the automated reviewer as an open-ended recursive review engine.

Wait for the normal automated review to complete and evaluate each finding independently.

---

# Phase 3 — Evaluate automated findings

For every automated finding, ask:

1. Does this show an acceptance criterion was not met?
2. Did this PR introduce or materially worsen a bug/regression?
3. Does this show the changed behavior does not work?

If **none** applies:

```text
Suggestion — Application Improvement Suggestions backlog
```

Do not correct the PR for that observation.

If one or more applies, it is a genuine current-PR blocker and may require correction.

When a validated finding exposes a directly related issue-scoped behavior family governed by the same demonstrated cause, use the bounded sibling-blocker semantics from `independent-implementation-review` before finalizing blocker disposition.

Do not accept automated severity labels, reviewer-generated scenarios, or blocker language without independently applying this contract.

Do not relitigate a previously adjudicated suggestion unless new evidence shows the current PR actually caused or materially worsened the problem.

---

# Phase 4 — Current-PR correction

If a genuine current-PR blocker exists:

1. Agent #1 corrects the validated blocker using the coherent correction-boundary rules defined by `application-implementation-workflow`, not merely the single reported permutation.
2. Agent #1 completes the full executable correction required by that coherent issue-scoped behavior boundary, including correction-scoped wiring, contracts, migrations, adapters, services, tests, configuration, and related work required for the corrected artifact to function as intended.
3. Agent #1 continues resolving build/test failures that are correction-scoped, issue-scoped, or introduced by the current artifact. Unrelated pre-existing failures must be evidenced and reported separately and do not require correction-scope expansion merely to achieve a globally green suite.
4. Agent #1 runs relevant validation and applies the universal pre-return completion gate from `application-implementation-workflow`.
5. Agent #1 may return control only at `READY FOR AGENT #2 RE-REVIEW` or `MATERIAL BLOCKER`.
6. Separately invoked Agent #2 performs the complete-artifact correction re-review under `independent-implementation-review` only after `READY FOR AGENT #2 RE-REVIEW`.
7. If that re-review finds another directly related sibling failure in the same behavioral subsystem, apply the churn-triggered bounded model/state-transition analysis before another correction.
8. Agent #2 answers:
   - Original acceptance criteria met?
   - New bug/regression introduced?
   - Does it work?
9. If the result is `Yes -> No -> Yes`, Agent #2 returns:
   `APPROVED — MERGE`
10. Agent #1 commits and pushes exactly the approved correction.
11. Confirm the pushed commit represents the artifact Agent #2 reviewed.
12. Because the production-bound PR head changed, run the bounded final-head automated review defined below.

Correction work must not expand into suggestions or unrelated cleanup.

Partial corrective progress, unresolved correction-scoped wiring, resolvable compile/build failures, resolvable correction-scoped test failures, or a list of remaining correction tasks are not valid reasons for Agent #1 to return control.

Diagnostic state models, scenario matrices, reviewer-generated examples, and automated examples remain evidence. They do not expand the original acceptance criteria.

---

# Phase 5 — Final-head automated artifact-integrity review

After Agent #2 returns `APPROVED — MERGE` for a validated blocker correction and Agent #1 commits/pushes exactly that approved correction, the production-bound PR head has changed.

Run **one bounded final-head automated/Codex review** on that new stable head.

This final-head review is an **artifact-integrity gate**, not permission for recursive automated-review churn.

Independently disposition every finding using the same three-question contract:

1. Is an original issue acceptance criterion unmet?
2. Did the current artifact introduce or materially worsen a bug/regression?
3. Does the implemented behavior fail to work?

Automated review remains evidence, not authority.

Diagnostic scenarios, reviewer-generated matrices, automated examples, and hardening ideas do not create new acceptance criteria.

## Anti-recursion rule

**Required:** one final-head automated review after a validated blocker correction changes the pushed production-bound PR head.

**Not required:** another automated review when no subsequent implementation correction changes the artifact.

**Forbidden:** blindly obeying automated findings, treating reviewer severity as disposition, or allowing automated review to expand acceptance criteria.

If the final-head automated review identifies another validated blocker and another correction materially changes the artifact, the normal production-bound sequence applies again because the artifact changed:

```text
final-head finding
  ↓
independent three-question disposition
  ↓
validated blocker?
  ├── No → suggestion / no implementation change
  └── Yes → Agent #1 coherent correction
               ↓
            Agent #2 complete-artifact re-review
               ↓
            Yes -> No -> Yes = APPROVED — MERGE
               ↓
            commit + push exactly approved correction
               ↓
            one bounded final-head automated review on new stable head
```

Do not run repeated automated reviews merely because an automated review occurred or because a finding was discussed. Another final-head review is tied to another implementation correction that materially changed the production-bound artifact.

---

# Phase 6 — Merge decision

The PR is ready to merge when:

- acceptance criteria are met;
- the current PR has not introduced an unresolved bug/regression;
- the changed behavior works;
- any genuine current-PR blockers found by automated review were corrected and re-approved by Agent #2 against the complete artifact;
- the pushed artifact matches the artifact Agent #2 approved;
- when a validated correction changed the pushed PR head, the required bounded final-head automated review has completed and no validated blocker remains;
- required CI/checks are acceptable;
- required human approval is present.

Return:

```text
APPROVED — MERGE
```

when the governing result is:

```text
Yes -> No -> Yes
```

Suggestions do not prevent merge.

---

# Promotion Protection

Promotion should remain intentionally boring.

That means:

- no planned feature implementation;
- no opportunistic refactoring;
- no acceptance-criteria completion;
- no hidden corrective feature-development cycle inside promotion.

New observations may still surface during promotion, but the same discipline applies: determine whether they are actually caused by the release artifact and whether they prevent the release from working as intended.

Do not convert unrelated application improvements into promotion work.

**Zero gum on the bottom of the shoe** means no known unresolved defect that actually prevents the approved release behavior from working safely as intended, not zero suggestions in the backlog.

---

# Direct-to-production implementation exceptions

A direct-to-production implementation PR uses the same three-question contract.

Because there is no integration buffer, required CI/human release controls still apply, but review does not become a general system audit.

Correct only blockers demonstrated by the current change.

---

# Automated reviewer discipline

Automated reviewers surface evidence. They do not own disposition.

Do not:

- treat every automated comment as mandatory work;
- convert suggestions into blockers because the reviewer uses strong wording;
- convert diagnostic scenarios or automated examples into new acceptance criteria;
- ask automated review to search broadly for unrelated production concerns;
- hold the PR open for preexisting or adjacent issues;
- run repeated automated reviews when no subsequent implementation correction changes the artifact.

Do:

- run one normal automated review on the stable initial implementation head;
- independently evaluate every finding against the three governing questions;
- correct only validated current-PR defects at their coherent issue-scoped behavior boundary;
- use separate complete-artifact Agent #2 re-review for those corrections;
- after Agent #2 approval and push of the exact approved correction, run one bounded final-head automated review because the production-bound artifact changed;
- if that final-head review validates another blocker and another correction changes the artifact, repeat the correction/re-review/final-head sequence for the newly changed artifact;
- merge when the stable final artifact satisfies `Yes -> No -> Yes`, required checks, and human approval.

---

# Workflow summary

```text
Agent #1 implementation
        ↓
separate Agent #2 complete-artifact review
        ↓
Yes -> No -> Yes?
   ├── No → coherent issue-scoped correction → Agent #2 re-review
   └── Yes
          ↓
      commit / push / PR
          ↓
      one normal automated review on stable initial head
          ↓
      independently disposition findings
          ↓
      validated current-PR blocker?
         ├── No → CI / human approval → MERGE
         └── Yes → Agent #1 coherent correction
                    ↓
                 universal pre-return completion gate
                    ├── MATERIAL BLOCKER
                    └── READY FOR AGENT #2 RE-REVIEW
                               ↓
                            Agent #2 complete-artifact re-review
                               ↓
                            directly related sibling failure?
                               ├── Yes → bounded churn analysis → coherent correction boundary
                               └── No
                               ↓
                            Yes -> No -> Yes = APPROVED — MERGE
                               ↓
                            commit / push exactly approved correction
                               ↓
                            one bounded final-head automated review
                               ↓
                            independently disposition findings
                               ↓
                            another validated blocker?
                               ├── No → CI / human approval → MERGE
                               └── Yes → repeat correction / re-review / final-head sequence
```

A final-head automated review occurs because a validated correction changed the production-bound artifact. It does not recurse when the artifact has not changed again.

---

# Definition of compliance

An integration PR follows this skill only when:

- review is governed by the three questions;
- one normal automated review runs on the stable implementation head;
- automated findings are independently evaluated rather than obeyed automatically;
- only original-acceptance failures, PR-introduced regressions, or non-working changed behavior require current correction;
- diagnostic scenarios, matrices, automated examples, and reviewer-generated tests do not create new acceptance criteria;
- suggestions do not hold the PR open;
- non-blocking observations route to `Application Improvement Suggestions` instead of automatically becoming standalone issues;
- Agent #1 applies required corrections under the global Agent #1 execution invariant and coherent correction-boundary rules from `application-implementation-workflow`;
- Agent #1 applies the universal pre-return completion gate and returns correction control only at `READY FOR AGENT #2 RE-REVIEW` or `MATERIAL BLOCKER`;
- unrelated pre-existing build/test failures do not force correction-scope expansion when they are evidenced separately;
- separately invoked Agent #2 re-reviews the complete corrected artifact only after `READY FOR AGENT #2 RE-REVIEW`;
- a directly related sibling failure after correction triggers bounded churn analysis before another correction;
- `Yes -> No -> Yes` after correction produces `APPROVED — MERGE`;
- a validated correction that changes the pushed production-bound PR head requires one bounded final-head automated review;
- repeated automated review is not required when no subsequent implementation correction changes the artifact;
- automated findings remain evidence, not authority;
- required CI and human approval still apply;
- promotion remains intentionally boring.
