---
name: integration-pr-review
description: Shorthand: pr-review. Review and gate an implementation pull request using the narrow three-question contract: acceptance criteria met, no new bug/regression introduced, and changed behavior works. Run one normal automated review on the stable implementation head, independently evaluate its findings, correct only genuine current-PR defects, use narrow Agent #2 re-review after correction, and avoid recursive automated-review loops.
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

Do not accept automated severity labels or blocker language without independently applying this contract.

Do not relitigate a previously adjudicated suggestion unless new evidence shows the current PR actually caused or materially worsened the problem.

---

# Phase 4 — Current-PR correction

If a genuine current-PR blocker exists:

1. Agent #1 corrects only that blocker under the Agent #1 execution invariant defined by `application-implementation-workflow`.
2. Agent #1 completes the full executable correction required by the blocker, including correction-scoped wiring, contracts, migrations, adapters, services, tests, configuration, and related work required for the corrected artifact to function as intended.
3. Agent #1 continues resolving build/test failures that are correction-scoped, issue-scoped, or introduced by the current artifact. Unrelated pre-existing failures must be evidenced and reported separately and do not require correction-scope expansion merely to achieve a globally green suite.
4. Agent #1 runs relevant validation and applies the universal pre-return completion gate from `application-implementation-workflow`.
5. Agent #1 may return control only at `READY FOR AGENT #2 RE-REVIEW` or `MATERIAL BLOCKER`.
6. Separately invoked Agent #2 performs the narrow correction re-review under `independent-implementation-review` only after `READY FOR AGENT #2 RE-REVIEW`.
7. Agent #2 answers:
   - Acceptance criteria met?
   - New bug/regression introduced?
   - Does it work?
8. If the result is `Yes -> No -> Yes`, Agent #2 returns:
   `APPROVED — MERGE`
9. Agent #1 commits and pushes the approved correction.
10. Confirm the pushed commit represents the artifact Agent #2 reviewed.
11. Merge the PR when required CI/human approval conditions are satisfied.

Correction work must not expand into suggestions or unrelated cleanup.

Partial corrective progress, unresolved correction-scoped wiring, resolvable compile/build failures, resolvable correction-scoped test failures, or a list of remaining correction tasks are not valid reasons for Agent #1 to return control.

---
# Phase 5 — No recursive automated review

**Do not automatically trigger another Codex/automated review merely because an approved correction changed the PR head.**

The previous rule requiring a fresh automated review after every substantive correction is retired.

After Agent #2 returns `APPROVED — MERGE` for the corrected artifact, the default path is:

```text
Agent #2 approved correction
        ↓
Agent #1 commit + push
        ↓
verify pushed artifact matches reviewed artifact
        ↓
required CI / human approval
        ↓
MERGE
```

A second automated review is warranted only when there is a **specific reason to believe the pushed artifact differs materially from what Agent #2 reviewed**, or when a repository rule explicitly requires another automated run.

Do not restart automated review simply because the head SHA changed as a consequence of the approved correction.

This prevents automated review from becoming recursive and surfacing a new adjacent observation on every head.

---

# Phase 6 — Merge decision

The PR is ready to merge when:

- acceptance criteria are met;
- the current PR has not introduced an unresolved bug/regression;
- the changed behavior works;
- any genuine current-PR blockers found by automated review were corrected and narrowly re-approved by Agent #2;
- the pushed artifact matches the artifact Agent #2 approved;
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

- recursively trigger review after every approved correction;
- treat every automated comment as mandatory work;
- convert suggestions into blockers because the reviewer uses strong wording;
- ask automated review to search broadly for unrelated production concerns;
- hold the PR open for preexisting or adjacent issues.

Do:

- run one normal automated review on the stable implementation head;
- evaluate every finding against the three questions;
- correct genuine current-PR defects;
- use separate narrow Agent #2 re-review for those corrections;
- merge after `Yes -> No -> Yes`.

---

# Workflow summary

```text
Agent #1 implementation
        ↓
separate Agent #2 narrow review
        ↓
Yes -> No -> Yes?
   ├── No → correct actual blocker → Agent #2 re-review
   └── Yes
          ↓
      commit / push / PR
          ↓
      one normal automated review
          ↓
      evaluate each finding against three questions
          ↓
      genuine current-PR blocker?
         ├── No → suggestion backlog
         └── Yes → Agent #1 full correction
                    ↓
                 universal pre-return completion gate
                    ├── MATERIAL BLOCKER
                    └── READY FOR AGENT #2 RE-REVIEW
                               ↓
                            separate Agent #2 narrow re-review
                               ↓
                            Yes -> No -> Yes = APPROVED — MERGE
                    ↓
                 commit / push
                    ↓
                 verify reviewed artifact == pushed artifact
                    ↓
                 CI / human approval
                    ↓
                 MERGE
```

No automatic second automated-review cycle follows the approved correction.

---

# Definition of compliance

An integration PR follows this skill only when:

- review is governed by the three questions;
- one normal automated review runs on the stable implementation head;
- automated findings are independently evaluated rather than obeyed automatically;
- only acceptance failures, PR-introduced regressions, or non-working changed behavior require current correction;
- suggestions do not hold the PR open;
- non-blocking observations route to `Application Improvement Suggestions` instead of automatically becoming standalone issues;
- Agent #1 applies required corrections under the global Agent #1 execution invariant from `application-implementation-workflow`;
- Agent #1 applies the universal pre-return completion gate and returns correction control only at `READY FOR AGENT #2 RE-REVIEW` or `MATERIAL BLOCKER`;
- unrelated pre-existing build/test failures do not force correction-scope expansion when they are evidenced separately;
- separately invoked Agent #2 narrowly re-reviews corrections only after `READY FOR AGENT #2 RE-REVIEW`;
- `Yes -> No -> Yes` after correction produces `APPROVED — MERGE`;
- a changed head after approved correction does not automatically trigger another automated review;
- another automated review occurs only for a concrete artifact-mismatch concern or explicit repository requirement;
- required CI and human approval still apply;
- promotion remains intentionally boring.
