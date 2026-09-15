---
name: independent-implementation-review
description: Perform the independent Agent #2 review of an uncommitted implementation artifact. Use only when Agent #2 has been invoked separately from Agent #1 after an explicit handoff; inspect the actual diff and runtime behavior, evaluate architecture, contracts, security, data access, failure handling, tests, regressions, and scope, classify concrete findings, and return Approved or Changes Required without modifying the implementation.
---

# Independent Implementation Review

## Purpose

This skill defines the canonical procedure for an independent engineering review of an implementation before it is committed, pushed, or opened as a pull request.

It exists to make Agent #2 review evidence-driven, repeatable, and technically meaningful rather than dependent on conversational summaries, reviewer style, model-specific habits, or self-review performed by the implementation agent.

This skill does not replace the governing application implementation workflow. It defines how the independent review step inside that workflow is performed.

---

# Applicability

Use this skill whenever an engineering workflow requires an independent review of implementation work before commit, push, or pull request creation.

The reviewer may be Codex, Claude, or another capable agent. The review responsibilities remain the same.

The implementation under review may span one repository or multiple repositories.

---

# Invocation boundary

This skill is an **Agent #2 skill**.

It must be invoked in a reviewer execution context that is separate from Agent #1's implementation execution.

A compliant Agent #2 review begins only after:

1. Agent #1 has completed its implementation pass;
2. Agent #1 has reached the mandatory no-commit/no-push/no-PR hold point;
3. Agent #1 has stopped;
4. control has returned to the workflow coordinator, builder, or calling environment;
5. Agent #2 has then been invoked separately to review the artifact.

**Agent #1 must not invoke this skill on its own sub-agent and count the result as the required independent review.**

A reviewer that is spawned, delegated, orchestrated, simulated, or controlled from inside Agent #1's execution does not satisfy this workflow's independence requirement.

The purpose of this boundary is not model diversity. Agent #1 and Agent #2 may use the same model product. The requirement is a real workflow handoff into a separately invoked review context.

If this skill is invoked from within Agent #1's implementation execution for the purpose of satisfying Agent #1's own review gate, stop and report that an external Agent #2 handoff is required.

---

# Core invariant

The reviewer must evaluate the actual implementation artifact independently of the implementer's confidence, summary, or claimed completeness.

A review is complete only when the reviewer has enough direct evidence to decide whether the implementation is fit to advance.

The reviewer must not modify the implementation while acting in the review role.

The reviewer must not be the implementation agent continuing under a different persona, role label, or internally spawned context.

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

Read the governing issue and determine intended behavior, explicit acceptance criteria, known constraints, declared out-of-scope behavior, and repositories or subsystems expected to change.

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

Determine what behavior, contracts, data flow, and execution paths changed and whether unrelated changes are present.

## 4. Trace changed behavior

Follow the affected behavior through the relevant layers, which may include UI, API boundaries, contracts, domain logic, authorization, persistence, background processing, external services, event/message flows, configuration, and operational failure paths.

Do not stop at the changed function if correctness depends on surrounding behavior.

## 5. Review architecture and repository-pattern consistency

Evaluate whether the implementation fits established architecture.

Look for misplaced responsibilities, layering violations, duplicated existing capability, bypassed abstractions, unnecessary new patterns, incorrect ownership boundaries, and coupling that creates avoidable regression risk.

A different implementation style is not a finding unless it creates a meaningful engineering problem or unjustified divergence.

## 6. Review contracts

Check relevant API, OpenAPI, request/response, domain, database, event/message, client/server, provider, nullability, enum, validation, and serialization contracts.

Look specifically for mismatches between capability/read models and execution paths.

## 7. Review authorization, security, and privacy

Where applicable, verify authentication assumptions, authorization boundaries, tenant/user ownership, privilege escalation risk, sensitive-data handling, secret exposure, unsafe trust of client-controlled values, and privacy-impacting behavior.

## 8. Review persistence and data-access behavior

Where applicable, inspect query correctness, transaction boundaries, N+1 behavior, unnecessary round trips, concurrency assumptions, idempotency, migration compatibility, partial-failure behavior, and stale or inconsistent state risks.

## 9. Review failure and operational behavior

Consider timeout behavior, cancellation propagation, retries, duplicate execution, partial success, provider failure, malformed input, unexpected state, logging, diagnosability, and operational recovery.

Do not require speculative complexity, but identify realistic failure paths that would create incorrect or unsafe behavior.

## 10. Review tests and validation

Evaluate whether validation demonstrates intended behavior rather than merely exercising implementation details.

Check the happy path, relevant edge cases, failure paths, authorization boundaries, contract behavior, regression coverage, integration behavior, and existing tests that may encode stale assumptions.

Passing tests do not override a confirmed implementation defect.

Missing tests are blocking when their absence leaves material behavior unprotected or project standards require them.

## 11. Review regression and adjacent-system effects

Inspect relevant surrounding behavior for unintended impact, including shared components, contracts, services, settings, feature gates, caching, notifications, downstream clients, and adjacent user workflows.

The goal is not exhaustive system audit. Review far enough to assess the realistic blast radius.

## 12. Review scope discipline

Identify implementation unrelated to the governing issue.

Unrelated cleanup, refactoring, speculative behavior, or opportunistic feature work should not be silently accepted merely because it is technically valid.

---

# Finding quality standard

Every substantive finding should state:

1. **Classification** — Blocking, Non-blocking, or Informational.
2. **Location** — file, component, function, contract, or execution path where relevant.
3. **Problem** — what is wrong or risky.
4. **Failure mechanism** — how the defect manifests or why the risk is real.
5. **Impact** — what behavior, user, contract, system, or operation is affected.
6. **Expected correction** — the required outcome without unnecessarily dictating implementation details.

Do not report vague findings without identifying the concrete failure being protected against.

---

# Finding classifications

## Blocking

A finding is blocking when the implementation should not advance because it is materially incorrect, unsafe, contract-breaking, authorization/security/privacy violating, operationally unacceptable, regression-causing, materially incomplete, or inconsistent with a required architectural invariant.

Blocking findings require correction and separate Agent #2 re-review.

## Non-blocking

A finding is non-blocking when it is legitimate and valuable but does not invalidate the implementation artifact or require correction before advancement.

Do not use Non-blocking as a place to park personal preferences.

## Informational

Informational notes provide useful context without requesting a change. Use them sparingly.

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

---

# Correction re-review

When Agent #1 returns corrections, the workflow must perform another handoff to a separately invoked Agent #2.

The reviewer must re-review the actual corrected artifact and:

1. verify each blocking finding was resolved;
2. inspect the correction diff;
3. assess whether corrections introduced new defects or regressions;
4. re-evaluate affected contracts and execution paths where necessary;
5. confirm no unrelated substantive changes were introduced;
6. return a new explicit verdict.

Agent #1 must not apply corrections and then invoke an internal reviewer to self-satisfy the re-review gate.

A correction can resolve the original symptom while creating a new failure elsewhere.

---

# Independence protections

To preserve independent review quality:

- Agent #1 must stop before Agent #2 is invoked;
- Agent #2 must be separately invoked after the handoff;
- do not treat Agent #1's explanation as proof;
- do not copy Agent #1's validation claims without checking relevant evidence;
- do not let prior approval of an approach substitute for review of its implementation;
- do not modify the code while acting as Agent #2;
- do not approve an artifact different from the one inspected;
- do not allow a substantive post-approval change to inherit the prior approval;
- do not satisfy independence by changing persona labels inside one implementation execution;
- do not satisfy independence through an Agent #1-controlled sub-agent or delegated reviewer.

If the artifact changes substantively after approval, it must be handed off and reviewed again before commit/push/PR.

---

# Review efficiency

Independent review should be deep, not wasteful.

Prioritize effort according to risk and changed behavior.

Do not perform unrelated repository archaeology merely to demonstrate thoroughness, require every possible validation command when focused evidence is sufficient, repeat expensive checks without reason, or serialize findings unnecessarily.

The objective is high signal: catch meaningful defects early without turning review into performative ceremony.

---

# Required output format

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

This skill governs the separately invoked Agent #2 review procedure used by `application-implementation-workflow`.

The parent workflow determines when the handoff occurs and what happens after the verdict. This skill determines how the review itself is performed.

```text
Agent #1 implementation
  ↓
NO COMMIT / NO PUSH / NO PR
  ↓
AGENT #1 STOPS
  ↓
separate Agent #2 invocation
  ↓
Independent Implementation Review
  ├── Changes Required
  │      ↓
  │   return control to Agent #1
  │      ↓
  │   Agent #1 correction pass
  │      ↓
  │   AGENT #1 STOPS
  │      ↓
  │   separate Agent #2 re-review
  │      └── repeat until Approved
  │
  └── Approved
         ↓
      return control to parent workflow
```

This skill does not authorize commit, push, PR creation, merge, or promotion. Those permissions remain with the governing workflow.

---

# Definition of review compliance

A review follows this skill only when:

- Agent #1 stopped before the review began;
- Agent #2 was invoked separately rather than spawned or simulated by Agent #1;
- the reviewer inspects the actual implementation artifact;
- the governing issue and relevant scope are understood;
- changed behavior is traced through the necessary surrounding system;
- architecture, contracts, security, data behavior, tests, regressions, and operational concerns are reviewed where applicable;
- findings describe concrete engineering risks rather than preferences;
- findings are classified by impact;
- the reviewer does not modify the implementation;
- the verdict is explicitly Approved or Changes Required;
- corrections are independently re-reviewed after another real handoff;
- substantive post-approval changes invalidate the previous approval;
- the review optimizes for meaningful defect detection rather than review theater.
