# Independent Implementation Review

## Purpose

This skill defines the canonical procedure for an independent engineering review of an implementation before it is committed, pushed, or opened as a pull request.

It exists to make Agent #2 review evidence-driven, repeatable, and technically meaningful rather than dependent on conversational summaries, reviewer style, or model-specific habits.

This skill does not replace the governing application implementation workflow. It defines how the independent review step inside that workflow is performed.

---

# Applicability

Use this skill whenever an engineering workflow requires an independent review of implementation work before commit, push, or pull request creation.

The reviewer may be Codex, Claude, or another capable agent. The review responsibilities remain the same.

The implementation under review may span one repository or multiple repositories.

---

# Core invariant

The reviewer must evaluate the actual implementation artifact independently of the implementer's confidence, summary, or claimed completeness.

A review is complete only when the reviewer has enough direct evidence to decide whether the implementation is fit to advance.

The reviewer must not modify the implementation while acting in the review role.

---

# Reviewer posture

The reviewer is an independent engineer, not a second implementer and not a stylistic critic.

The reviewer must:

- inspect evidence directly;
- trace changed behavior far enough to understand its runtime effect;
- distinguish correctness risks from preferences;
- identify concrete failure mechanisms;
- avoid inventing findings merely to appear thorough;
- avoid expanding issue scope without a legitimate correctness, safety, contract, or regression reason;
- classify findings by impact;
- return an explicit verdict.

The reviewer must not approve solely because tests pass, the code compiles, or the implementation appears plausible.

---

# Required review inputs

Before reviewing, establish the available source of truth for:

- governing issue or acceptance criteria;
- current repository state;
- implementation diff or working-tree changes;
- relevant architecture and repository patterns;
- tests and validation results;
- contracts and adjacent execution paths affected by the change.

If material inputs are unavailable and the missing information prevents responsible review, stop and report that the review cannot be completed yet.

Do not guess around missing evidence.

---

# Review sequence

Perform the review in the following order unless the repository or change requires a justified variation.

## 1. Establish scope

Read the governing issue and determine:

- intended behavior;
- explicit acceptance criteria;
- known constraints;
- declared out-of-scope behavior;
- repositories or subsystems expected to change.

Do not begin by trusting the implementation summary as the definition of scope.

## 2. Inspect repository state

Confirm what is actually under review.

Inspect, as applicable:

- current branch;
- working-tree status;
- staged and unstaged changes;
- untracked files;
- relevant repository conventions;
- unexpected unrelated changes.

The review verdict applies only to the inspected artifact.

## 3. Inspect the full implementation diff

Review the actual changes, not only selected files or the implementer's narrative.

Determine:

- what behavior changed;
- what contracts changed;
- what data flow changed;
- what execution paths were added, removed, or altered;
- whether unrelated changes are present.

## 4. Trace changed behavior

Follow the affected behavior through the relevant layers.

Depending on the system, this may include:

- UI and interaction flow;
- API boundary;
- request/response contracts;
- domain logic;
- authorization and identity;
- persistence and queries;
- background processing;
- external services;
- event/message flows;
- configuration;
- operational failure paths.

Do not stop at the changed function if correctness depends on surrounding behavior.

## 5. Review architecture and repository-pattern consistency

Evaluate whether the implementation fits the system's established architecture.

Look for:

- misplaced responsibilities;
- layering violations;
- duplicated existing capability;
- bypassed abstractions;
- unnecessary new patterns;
- incorrect ownership boundaries;
- coupling that creates avoidable regression risk.

A different implementation style is not a finding unless it creates a meaningful engineering problem or unjustified divergence.

## 6. Review contracts

Check all relevant contracts for consistency.

Examples include:

- API and OpenAPI contracts;
- request and response models;
- domain invariants;
- database schema expectations;
- event/message contracts;
- client/server assumptions;
- external-provider constraints;
- nullability, enum, validation, and serialization behavior.

Look specifically for mismatches between capability/read models and execution paths.

## 7. Review authorization, security, and privacy

Where applicable, verify:

- authentication assumptions;
- authorization boundaries;
- tenant/user ownership;
- privilege escalation risk;
- sensitive-data handling;
- secret exposure;
- unsafe trust of client-controlled values;
- privacy-impacting behavior.

Security review must be proportional to the change, but security-sensitive paths must not be skipped merely because the issue did not mention security explicitly.

## 8. Review persistence and data-access behavior

Where applicable, inspect:

- query correctness;
- transaction boundaries;
- N+1 behavior;
- duplicate or unnecessary round trips;
- concurrency assumptions;
- idempotency;
- migration compatibility;
- partial-failure behavior;
- stale or inconsistent state risks.

## 9. Review failure and operational behavior

Determine what happens when dependencies or assumptions fail.

Consider:

- timeout behavior;
- cancellation propagation;
- retries;
- duplicate execution;
- partial success;
- provider failure;
- malformed input;
- unexpected state;
- logging and diagnosability;
- operational recovery.

Do not require speculative complexity where the system does not need it, but do identify realistic failure paths that would create incorrect or unsafe behavior.

## 10. Review tests and validation

Evaluate whether validation demonstrates the intended behavior rather than merely exercising implementation details.

Check, as appropriate:

- happy path;
- relevant edge cases;
- failure paths;
- authorization boundaries;
- contract behavior;
- regression coverage;
- integration behavior;
- existing tests that may now encode stale assumptions.

Passing tests do not override a confirmed implementation defect.

Missing tests are blocking when the absence leaves material behavior unprotected or the project standards require them.

## 11. Review regression and adjacent-system effects

Inspect the most relevant surrounding behavior for unintended impact.

Consider:

- shared components;
- shared contracts;
- reused services;
- global settings;
- feature gates;
- caching;
- notification behavior;
- downstream clients;
- user workflows adjacent to the change.

The goal is not exhaustive system audit. Review far enough to assess the realistic blast radius of the implementation.

## 12. Review scope discipline

Identify implementation that is unrelated to the governing issue.

Unrelated cleanup, refactoring, speculative behavior, or opportunistic feature work should not be silently accepted merely because it is technically valid.

Scope findings should be based on engineering and delivery risk, not rigid line-count minimization.

---

# Finding quality standard

Every substantive finding should contain enough information for Agent #1 to understand and evaluate it without guessing.

A finding should state:

1. **Classification** — Blocking, Non-blocking, or Informational.
2. **Location** — file, component, function, contract, or execution path where relevant.
3. **Problem** — what is wrong or risky.
4. **Failure mechanism** — how the defect manifests or why the risk is real.
5. **Impact** — what behavior, user, contract, system, or operation is affected.
6. **Expected correction** — the required outcome, without unnecessarily dictating implementation details.

Do not report vague findings such as "consider improving error handling" without identifying the concrete failure being protected against.

---

# Finding classifications

## Blocking

A finding is blocking when the implementation should not advance in its current state because it is materially:

- incorrect;
- unsafe;
- contract-breaking;
- authorization/security/privacy violating;
- operationally unacceptable;
- regression-causing;
- materially incomplete against the issue;
- inconsistent with a required architectural invariant.

Blocking findings require correction and re-review.

## Non-blocking

A finding is non-blocking when it is legitimate and valuable but does not invalidate the implementation artifact or require correction before it advances.

Examples may include maintainability improvements, minor hardening, or follow-up opportunities outside the acceptance boundary.

Do not use Non-blocking as a place to park personal preferences.

## Informational

Informational notes provide useful context without requesting a change.

Use them sparingly.

---

# Review verdict

The review must end with exactly one of these verdicts:

## Approved

Use **Approved** only when no blocking findings remain.

Approval means the reviewed artifact is fit to advance to the next step in the governing implementation workflow.

Approval does not grant permission to make substantive changes afterward without further review.

## Changes Required

Use **Changes Required** when one or more blocking findings remain.

List all known blocking findings from the completed review so Agent #1 can perform a coherent correction pass rather than discovering avoidable blockers one correction at a time.

A reviewer is not required to invent additional findings merely to justify this verdict.

---

# Correction re-review

When Agent #1 returns corrections, re-review the actual corrected artifact.

The re-review must:

1. verify each blocking finding was actually resolved;
2. inspect the correction diff;
3. assess whether corrections introduced new defects or regressions;
4. re-evaluate affected contracts and execution paths where necessary;
5. confirm no unrelated substantive changes were introduced;
6. return a new explicit verdict.

Do not limit re-review to checking whether the changed line now resembles the requested fix.

A correction can resolve the original symptom while creating a new failure elsewhere.

---

# Independence protections

To preserve independent review quality:

- do not treat Agent #1's explanation as proof;
- do not copy Agent #1's validation claims without checking relevant evidence;
- do not let prior approval of an approach substitute for review of its implementation;
- do not modify the code while acting as Agent #2;
- do not approve an artifact different from the one inspected;
- do not allow a substantive post-approval change to inherit the prior approval.

If the artifact changes substantively after approval, it must be reviewed again before commit/push/PR.

---

# Review efficiency

Independent review should be deep, not wasteful.

The reviewer should prioritize review effort according to risk and changed behavior.

Do not perform unrelated repository archaeology merely to demonstrate thoroughness.

Do not require every possible validation command when focused evidence is sufficient.

Do not repeat expensive checks without a reason.

Do consolidate findings from the current artifact into a coherent review whenever possible.

The objective is high signal: catch meaningful defects early without turning review into performative ceremony.

---

# Required output format

Use this structure unless the governing workflow specifies an equivalent format:

```markdown
# Independent Implementation Review

## Verdict
Approved | Changes Required

## Artifact reviewed
- Branch/state:
- Scope:
- Relevant diff/repositories:

## Findings

### Blocking
- None.

### Non-blocking
- None.

### Informational
- None.

## Validation and evidence reviewed
- ...

## Review summary
Concise explanation of why the artifact is or is not fit to advance.
```

For each substantive finding, include the location, failure mechanism, impact, and expected correction.

---

# Relationship to the application implementation workflow

This skill governs the Agent #2 independent-review procedure used by the canonical application implementation workflow.

The parent workflow determines when review occurs and what happens after the verdict.

This skill determines how the review itself is performed.

In the normal flow:

```text
Agent #1 implementation
  ↓
NO COMMIT / NO PUSH / NO PR
  ↓
Independent Implementation Review
  ├── Changes Required
  │      ↓
  │   Agent #1 correction pass
  │      ↓
  │   Independent re-review
  │      └── repeat until Approved
  │
  └── Approved
         ↓
      parent workflow may advance
```

This skill does not authorize commit, push, PR creation, merge, or promotion. Those permissions remain with the governing workflow.

---

# Definition of review compliance

A review follows this skill only when:

- the reviewer inspects the actual implementation artifact;
- the governing issue and relevant scope are understood;
- changed behavior is traced through the necessary surrounding system;
- architecture, contracts, security, data behavior, tests, regressions, and operational concerns are reviewed where applicable;
- findings describe concrete engineering risks rather than preferences;
- findings are classified by impact;
- the reviewer does not modify the implementation;
- the verdict is explicitly Approved or Changes Required;
- corrections are independently re-reviewed;
- substantive post-approval changes invalidate the previous approval;
- the review optimizes for meaningful defect detection rather than review theater.
