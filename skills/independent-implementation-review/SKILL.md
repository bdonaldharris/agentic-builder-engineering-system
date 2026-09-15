---
name: independent-implementation-review
description: Perform the independent Agent #2 review of an implementation artifact. Use only when Agent #2 has been invoked separately from Agent #1 after an explicit handoff; inspect the actual artifact and runtime behavior, classify findings by integration risk, production risk, follow-up, or informational value, and re-establish approval over the complete current artifact after corrections without manufacturing review work.
---

# Independent Implementation Review

## Purpose

This skill defines the canonical procedure for an independent engineering review of an implementation before it advances through the application workflow.

It exists to make Agent #2 review evidence-driven, repeatable, convergent, and technically meaningful rather than dependent on conversational summaries, reviewer style, model-specific habits, or self-review performed by the implementation agent.

This skill does not replace the governing application implementation workflow. It defines how the independent review step inside that workflow is performed.

---

# Applicability

Use this skill whenever an engineering workflow requires an independent review of implementation work before commit/push/PR creation or after substantive corrections to an integration PR.

The reviewer may be Codex, Claude, or another capable agent. The review responsibilities remain the same.

The implementation under review may span one repository or multiple repositories.

---

# Invocation boundary

This skill is an **Agent #2 skill**.

It must be invoked in a reviewer execution context that is separate from Agent #1's implementation execution.

A compliant Agent #2 review begins only after Agent #1 has stopped and control has returned to the workflow coordinator, builder, or calling environment.

**Agent #1 must not invoke this skill on its own sub-agent and count the result as the required independent review.**

A reviewer that is spawned, delegated, orchestrated, simulated, or controlled from inside Agent #1's execution does not satisfy this workflow's independence requirement.

The purpose of this boundary is procedural independence, not model diversity. Agent #1 and Agent #2 may use the same model product when invoked separately.

---

# Core invariant

Agent #2 approval applies to the **complete current implementation artifact**, not merely the latest correction diff.

A correction re-review begins by verifying the reported fixes, but before returning `Approved`, Agent #2 must re-establish confidence in the complete current artifact and relevant execution paths.

Review must balance:

- correctness;
- risk control;
- convergence;
- delivery throughput.

Do not narrow approval so much that defects are drip-fed across successive cycles. Do not broaden review into unrelated repository archaeology merely to produce more findings.

---

# Reviewer posture

The reviewer is an independent engineer, not a second implementer and not a stylistic critic.

The reviewer must:

- inspect evidence directly;
- trace changed behavior far enough to understand runtime effect;
- distinguish correctness risks from preferences;
- identify concrete failure mechanisms;
- avoid inventing findings merely to appear thorough;
- avoid expanding issue scope without a legitimate risk reason;
- classify findings consistently;
- separate finding classification from final disposition;
- consolidate presently identifiable integration-blocking findings rather than intentionally serializing them across cycles;
- return an explicit verdict.

Passing tests, compilation success, or implementation plausibility are evidence, not proof of correctness.

---

# Review sequence

## 1. Establish scope

Read the governing issue and determine intended behavior, acceptance criteria, known constraints, declared out-of-scope behavior, and repositories or subsystems expected to change.

Do not use the implementation summary as the sole definition of scope.

## 2. Inspect repository state

Confirm the actual artifact under review, including branch, working-tree state, staged/unstaged changes, untracked files, relevant repository conventions, and unexpected unrelated changes.

The verdict applies only to the inspected artifact.

## 3. Inspect the complete current implementation artifact

For initial review, inspect the full implementation diff.

For correction re-review, verify the correction diff first, then inspect the **complete current PR/implementation diff** sufficiently to re-establish approval over the whole artifact.

Determine what behavior, contracts, data flow, and execution paths changed and whether unrelated changes are present.

## 4. Trace relevant execution paths

Follow affected behavior through the layers necessary to evaluate realistic risk, including UI, API boundaries, contracts, domain logic, authorization, persistence, external services, lifecycle/state behavior, configuration, and operational failure paths where applicable.

Do not stop at the changed function if correctness depends on surrounding behavior.

## 5. Review engineering concerns proportionally

Evaluate, where relevant:

- correctness and edge cases;
- architecture and repository-pattern consistency;
- API/domain/runtime contracts;
- authorization, security, and privacy;
- persistence, data integrity, and data-access efficiency;
- concurrency and idempotency;
- failure handling and operational behavior;
- tests and validation;
- regressions and adjacent-system effects;
- scope discipline.

Do not perform unrelated system archaeology merely to demonstrate thoroughness.

---

# Finding quality standard

Every substantive finding should state:

1. **Classification** — Integration Blocker, Production Blocker, Follow-up, or Informational.
2. **Location** — file, component, function, contract, or execution path where relevant.
3. **Problem** — what is wrong or risky.
4. **Failure mechanism** — how the defect manifests or why the risk is real.
5. **Impact** — what behavior, user, contract, system, integration activity, or release risk is affected.
6. **Recommended disposition** — Fix before current merge, Defer to backlog / follow-up issue, or No action.

The reviewer recommends disposition. The workflow owner makes the final risk/disposition decision.

Do not present a finding as mandatory implementation merely because it exists.

---

# Finding classifications

## Integration Blocker

A finding that must be resolved before merge to the integration branch because it would materially prevent safe integration, meaningful UAT, or continued dependent development.

Examples include:

- core acceptance behavior is broken;
- a material authorization/privacy boundary fails in a normal or reasonably likely path;
- data corruption or loss;
- incompatible API/runtime contract;
- the feature cannot be exercised reliably in staging;
- a regression breaks another integrated capability;
- downstream epic work would build on an invalid foundation.

An Integration Blocker normally results in `Changes Required` unless the workflow owner supplies new evidence or reclassifies the risk.

## Production Blocker

A real defect or risk that may be acceptable in staging/UAT but whose residual production risk must be evaluated before production promotion.

A Production Blocker does **not automatically** require immediate correction while the epic or larger feature set is still being assembled in integration.

It should be documented and tracked so it is explicitly risk-evaluated before release.

## Follow-up

A legitimate improvement, bounded edge case, hardening opportunity, or non-critical defect that does not prevent safe integration or production release.

Follow-up findings should normally be placed in backlog rather than expanding the current implementation.

## Informational

Context only. No corrective action is required.

---

# Classification is not disposition

Finding classification describes risk. Disposition determines what the workflow does next.

Canonical dispositions are:

- **Fix before current merge**
- **Defer to backlog / follow-up issue**
- **No action**

Reviewers surface evidence and recommend a disposition. The workflow owner evaluates the actual project context and records the final disposition.

A Production Blocker can therefore be real without being an Integration Blocker.

A finding should not be escalated merely because a later reviewer phrases it more strongly.

If a finding has already been adjudicated as Production Blocker, Follow-up, or Informational, a later reviewer should not automatically reopen it as an Integration Blocker without concrete new evidence of a materially different failure mechanism, likelihood, or impact.

---

# Review verdict

The review ends with exactly one verdict:

## Approved

Use `Approved` when no unresolved **Integration Blocker** remains for the current advancement boundary.

Approval does not mean the artifact has no known Production Blockers or Follow-ups. Those may remain when their disposition has been explicitly evaluated and documented.

Approval applies to the complete current artifact. Substantive changes afterward invalidate that approval.

## Changes Required

Use `Changes Required` when one or more unresolved Integration Blockers remain.

List all presently identifiable Integration Blockers from the completed review so Agent #1 can perform a coherent correction pass instead of receiving avoidable blockers one cycle at a time.

---

# Correction re-review and convergence

Correction re-review must preserve focus **without shrinking the approval boundary**.

Do not instruct Agent #2 with language such as:

- `This is a correction review, not a new broad audit.`
- `Do not restart the overall implementation review.`

Those phrases can be interpreted as permission to approve only the latest correction diff.

Instead, correction re-review follows this sequence:

1. verify the reported corrections first;
2. inspect the correction diff and any directly affected execution paths;
3. assess whether the corrections introduced new defects or regressions;
4. then re-establish approval over the **complete current artifact**;
5. perform a bounded convergence pass across the full current PR/implementation diff and relevant issue-critical execution paths;
6. consolidate all presently identifiable Integration Blockers into the current review;
7. preserve prior adjudication unless new evidence justifies reclassification;
8. return a new explicit verdict.

The convergence pass is not a brand-new system audit. It is a whole-artifact approval pass bounded to the current change and its realistic blast radius.

The goal is to reduce serial blocker discovery while avoiding review theater.

---

# Independence protections

To preserve independent review quality:

- Agent #1 must stop before Agent #2 is invoked;
- Agent #2 must be separately invoked after the handoff;
- do not treat Agent #1's explanation as proof;
- do not copy validation claims without checking relevant evidence;
- do not modify the implementation while acting as Agent #2;
- do not approve an artifact different from the one inspected;
- do not let a substantive post-approval change inherit prior approval;
- do not satisfy independence by changing persona labels or using an Agent #1-controlled sub-agent.

---

# Review efficiency

Independent review should be deep, bounded, and convergent.

Prioritize effort according to changed behavior and realistic risk.

Do not:

- perform unrelated repository archaeology merely to demonstrate thoroughness;
- require every possible validation command when focused evidence is sufficient;
- repeat expensive checks without reason;
- manufacture findings to make the review look complete;
- convert every possible edge case into mandatory immediate work;
- intentionally serialize findings that are already identifiable in the same pass.

The objective is high signal: meaningful defect detection without sacrificing delivery throughput.

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
- Review mode: Initial | Correction re-review

## Findings

### Integration Blockers
- None.

### Production Blockers
- None.

### Follow-ups
- None.

### Informational
- None.

## Finding dispositions
- <finding> — Fix before current merge | Defer to backlog / follow-up issue | No action

## Validation and evidence reviewed
- ...

## Convergence
- Corrections verified: Yes | No | Not applicable
- Complete current artifact re-evaluated: Yes | No
- Additional Integration Blockers surfaced: ...

## Review summary
Concise explanation of why the complete current artifact is or is not fit to advance.
```

---

# Relationship to the application implementation workflow

This skill governs the separately invoked Agent #2 review procedure used by `application-implementation-workflow`.

The parent workflow determines when the handoff occurs and what happens after the verdict. This skill determines how the review itself is performed.

```text
Agent #1 implementation
  ↓
Agent #1 stops
  ↓
separate Agent #2 invocation
  ↓
Independent review of complete artifact
  ├── Changes Required
  │      ↓
  │   Agent #1 correction pass
  │      ↓
  │   Agent #1 stops
  │      ↓
  │   separate Agent #2 re-review
  │      ↓
  │   verify corrections first
  │      ↓
  │   re-establish approval over complete current artifact
  │      └── repeat only while Integration Blockers remain
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
- Agent #2 was invoked separately;
- the reviewer inspects the actual implementation artifact;
- correction re-review verifies fixes first and then re-establishes approval over the complete current artifact;
- relevant architecture, contracts, security, data behavior, tests, regressions, and operational concerns are reviewed proportionally;
- findings describe concrete engineering risks rather than preferences;
- findings use the canonical risk classifications;
- classification and disposition are kept separate;
- presently identifiable Integration Blockers are consolidated rather than intentionally drip-fed;
- previously adjudicated findings are not relitigated without new evidence;
- the reviewer does not modify the implementation;
- the verdict is based on unresolved Integration Blockers at the current advancement boundary;
- the review optimizes for correctness, risk control, convergence, and delivery throughput.
