---
name: independent-implementation-review
description: Perform the independent Agent #2 review of an implementation artifact using the narrow three-question review contract: acceptance criteria met, no new bug/regression introduced, and implemented behavior works. Use only after a real handoff from Agent #1. Review enough surrounding code to answer those questions, route unrelated observations to the consolidated Application Improvement Suggestions backlog, and do not turn review into an open-ended system audit.
---

# Independent Implementation Review

## Purpose

This skill defines the canonical Agent #2 review procedure for application implementation work.

The purpose of review is to determine whether the **current implementation is correct for the issue**, not to audit the application for every defect or improvement that can be discovered while tracing the change.

The governing review contract is intentionally narrow.

---

# Governing review contract

Every implementation review answers exactly three governing questions:

1. **Acceptance criteria met?**
2. **New bug/regression introduced?**
3. **Does it work?**

The approval rule is:

```text
Yes -> No -> Yes = APPROVED
```

For a correction re-review performed on an integration PR:

```text
Yes -> No -> Yes = APPROVED — MERGE
```

Anything that does not cause one of those three answers to fail is not a blocker for the current implementation.

---

# Invocation boundary

This is an **Agent #2 skill**.

Agent #2 must be invoked separately after Agent #1 stops and returns control to the builder, coordinator, or calling environment.

Agent #1 must not satisfy the independent-review gate by spawning, simulating, impersonating, or internally controlling its own reviewer.

The requirement is procedural independence, not model diversity.

Agent #2 reviews the actual implementation artifact, not merely Agent #1's summary.

---

# Review scope

Review the complete current implementation artifact sufficiently to answer the three governing questions.

The reviewer may inspect surrounding code, contracts, tests, architecture, persistence, authorization, integrations, and execution paths when necessary to determine:

- whether the issue acceptance criteria were met;
- whether the current change introduced or materially worsened a bug/regression;
- whether the implemented behavior actually works.

The reviewer must **not** turn this into a general audit of the application.

Do not search indefinitely for unrelated defects, historical weaknesses, speculative future risks, cleanup opportunities, or architecture improvements.

Review rigor means answering the three governing questions with evidence. It does not mean enumerating everything that could be improved in the system.

---

# Current implementation blockers

A finding blocks the current implementation only when it demonstrates at least one of these:

- an acceptance criterion was not met;
- the current change introduced or materially worsened a bug or regression;
- the implemented behavior does not work as intended.

A blocker should identify:

1. the affected governing question;
2. the relevant location or execution path;
3. the concrete failure mechanism;
4. the observed or reasonably demonstrated impact;
5. the correction outcome required.

Do not classify a finding as blocking merely because it is technically valid, security-related, architectural, or worth improving.

---

# Suggestions

The following are suggestions unless the current implementation directly caused or materially worsened them:

- preexisting bugs;
- preexisting architectural weaknesses;
- historical-data or deployment cleanup;
- future hardening;
- adjacent improvements;
- broader contract completeness unrelated to the changed behavior;
- unrelated test gaps;
- refactoring opportunities;
- consistency improvements;
- performance improvements outside the changed execution path;
- speculative concurrency concerns not introduced by the change;
- unrelated security observations;
- "while tracing this code I noticed..." findings.

Suggestions must **not** change the approval decision.

Do not classify these as Integration Blockers or Production Blockers for the current implementation.

---

# Consolidated suggestion backlog

Do not automatically create a standalone GitHub issue for each non-blocking review observation.

Each application repository should maintain one persistent issue named:

`Application Improvement Suggestions`

Non-blocking observations should be directed to that consolidated backlog for the repository where the observation belongs.

A suggestion entry should remain lightweight:

- originating issue/PR;
- short description;
- why it may be worth considering;
- review-comment reference when available.

Adding a suggestion does not imply severity, priority, production gating, commitment to implement, or creation of a separate implementation issue.

A suggestion becomes its own issue only when the builder/workflow owner deliberately promotes it into planned work.

---

# Initial review procedure

1. Read the governing issue and acceptance criteria.
2. Inspect the actual current implementation diff/artifact.
3. Trace only the surrounding execution paths needed to evaluate the changed behavior.
4. Run or inspect appropriate validation evidence.
5. Answer the three governing questions.
6. Report all presently identifiable **current-implementation blockers** together.
7. Record worthwhile non-blocking observations under **Suggestions** without expanding the implementation scope.
8. Return the explicit verdict.

Keep the existing anti-drip-feeding rule for blockers: if multiple concrete reasons currently make one of the three governing questions fail, report them in the same pass when reasonably identifiable.

That rule does not require enumerating every possible application improvement.

---

# Correction re-review

Correction re-review is intentionally narrow.

Agent #2 must:

1. inspect the corrected artifact;
2. verify the reported blocker was corrected;
3. inspect the complete current implementation sufficiently to answer the three governing questions again;
4. determine whether the correction itself introduced a new bug/regression;
5. return the three answers and verdict.

Do not restart an open-ended architecture audit.

Do not search for unrelated defects merely because the PR has changed.

If the answers are:

```text
Acceptance criteria met? Yes
New bug/regression introduced? No
Does it work? Yes
```

return:

```text
APPROVED
```

For an integration-PR correction re-review, return:

```text
APPROVED — MERGE
```

Suggestions discovered during re-review do not prevent approval.

---

# Architecture and ambiguity protections

This narrow review contract does not remove existing engineering discipline.

Where relevant to the changed behavior, verify that the implementation did not create an unnecessary parallel architecture when an established owner/pattern already exists.

When the **current implementation** creates or modifies a Supabase table or migration used through the Data API, inspect the relevant PostgreSQL grants and RLS path only far enough to answer the three governing questions.

A missing required grant is a current implementation blocker only when it causes one of the governing questions to fail, for example when:

- an acceptance criterion requiring Data API access is not met;
- the current change introduces a regression in the intended access path;
- the implemented Data API behavior does not work because the intended role lacks the required table operation.

Do not audit unrelated historical Supabase tables or perform broad database-permission archaeology. Preexisting grants, missing grants, or RLS policies outside the current change are suggestions or out of scope unless the current implementation demonstrably affects them.

Remember that PostgreSQL grants and RLS are separate layers: a role may have an RLS policy but still lack the table-level operation grant needed to reach that policy, or it may have a table grant while RLS still restricts rows.

If material ambiguity in the issue, design, or acceptance criteria prevents responsible review, ask focused clarification questions instead of guessing.

Do not manufacture ambiguity for minor implementation details that established repository patterns already resolve.

---

# Required output format

```markdown
# Independent Implementation Review

## Acceptance criteria met?
Yes | No
Evidence:
- ...

## New bug/regression introduced?
Yes | No
Evidence:
- ...

## Does it work?
Yes | No
Evidence:
- ...

## Current implementation blockers
- None.
—or—
- <blocker tied to one of the three governing questions>

## Suggestions
- None.
—or—
- <lightweight non-blocking observation for Application Improvement Suggestions>

## Verdict
APPROVED | CHANGES REQUIRED

For integration-PR correction re-review:
APPROVED — MERGE | CHANGES REQUIRED
```

A reviewer may include concise validation evidence, but the output should stay centered on the three governing questions.

---

# Independence protections

To preserve independent review quality:

- Agent #1 stops before Agent #2 begins;
- Agent #2 is separately invoked;
- Agent #2 inspects the actual artifact;
- Agent #2 does not modify implementation while reviewing;
- Agent #1's summary is context, not proof;
- substantive changes after approval invalidate that approval for the changed artifact;
- findings are independently evaluated rather than accepted merely because another reviewer emitted them.

---

# Review efficiency

Review should be rigorous, bounded, and productive.

Do not:

- conduct exhaustive application audits;
- perform unrelated repository archaeology;
- search for every possible production concern;
- speculate about future failures not caused by the change;
- expand architecture review beyond what is needed to evaluate the issue;
- turn every observation into a blocker;
- create a standalone follow-up issue for every observation;
- hold the implementation open for suggestions.

The goal is to determine whether the current change met its contract without introducing a new defect.

---

# Definition of review compliance

A review follows this skill only when:

- Agent #2 is independently invoked;
- the actual implementation artifact is reviewed;
- the decision is based on the three governing questions;
- only acceptance failures, change-introduced regressions, or non-working implemented behavior block advancement;
- surrounding code is inspected only as far as needed to answer those questions;
- all presently identifiable current-implementation blockers are reported together when reasonably possible;
- suggestions do not alter approval;
- non-blocking observations are routed toward the consolidated `Application Improvement Suggestions` backlog rather than automatically becoming standalone issues;
- correction re-review remains narrow and returns `APPROVED — MERGE` when the PR satisfies `Yes -> No -> Yes`;
- the reviewer does not modify the implementation.
