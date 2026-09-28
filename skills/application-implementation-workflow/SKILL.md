---
name: application-implementation-workflow
description: Shorthand: implement-workflow. Govern the application implementation lifecycle with scoped Agent #1 implementation, independent Agent #2 review, issue-bound corrections, one normal automated PR review, and boring promotion. Inspect existing architecture before inventing new structures, ask when material requirements are ambiguous, and use the three-question review contract without expanding work into unrelated application auditing.
---

# Application Implementation Workflow

## Purpose

This skill defines the canonical implementation lifecycle for application software constructed with AI agents.

It keeps implementation scoped to the governing issue while preserving independent review and practical delivery throughput.

## Invocation model

The governing issue is the authoritative source of change-specific requirements, acceptance criteria, scope, constraints, and implementation context.

This skill owns the reusable implementation procedure. The invocation prompt does not need to restate issue content or workflow rules already defined here.

When the agent is already operating in the correct repository workspace, a minimal invocation is sufficient, for example:

```text
implement #1482 using implement-workflow
```

Resolve `implement-workflow` to this canonical skill, then retrieve/read the governing issue and execute this workflow. Do not require the prompt to duplicate the issue specification, branch rules, review rules, or recurring `DO NOT` instructions owned by this skill.

The governing review contract is:

1. **Acceptance criteria met?**
2. **New bug/regression introduced?**
3. **Does it work?**

```text
Yes -> No -> Yes = APPROVED
```

For an integration PR:

```text
Yes -> No -> Yes = MERGE
```

---

# Inspect before inventing

Before creating a new abstraction, service, component, integration path, domain construct, storage pattern, workflow, or architectural convention, inspect the existing application for:

- the current owner of the responsibility;
- similar or adjacent behavior;
- reusable abstractions;
- established repository patterns;
- existing contracts and extension points.

Prefer extending the established owning system over creating parallel capability unless a new boundary is actually required.

Do not introduce something new merely because it is locally convenient.

---

# Ambiguity and clarification invariant

**Do not guess through material ambiguity. Ask before inventing.**

If requirements, design intent, acceptance criteria, architecture ownership, contracts, data semantics, authorization, integration behavior, scope, or user experience are materially ambiguous, Agent #1 may return for clarification only when the ambiguity is material and actually prevents further safe execution.

Before returning, Agent #1 must attempt to resolve the ambiguity from the governing issue, current repository, existing architecture and established repository patterns, established product or engineering decisions, and authoritative external/provider documentation when applicable.

Minor implementation details do not justify returning control when an established repository pattern provides a clear answer and the choice does not materially change behavior or scope.

Once a material blocker or ambiguity is resolved, Agent #1 resumes the interrupted implementation or correction cycle. Resolution of the blocker does not create a new progress-report terminal state.

---

# Agent #1 execution invariant

**Incomplete work is not a response state.**

This invariant applies anywhere Agent #1 owns executable implementation or correction work, including:

- initial implementation;
- internal milestones during implementation;
- compile/build failure correction;
- test failure diagnosis and correction;
- implementation resumed after a material blocker is resolved;
- Agent #2 correction cycles;
- Agent #2 re-review correction cycles;
- automated/Codex review corrections owned by Agent #1.

**Agent #1 must not voluntarily return control while executable issue-scoped implementation or validated correction work remains and no material blocker exists.**

Agent #1 must continue resolving build/test failures that are issue-scoped, correction-scoped, or introduced by the current artifact. Unrelated pre-existing failures must be evidenced and reported separately and do not require Agent #1 to expand scope merely to achieve a globally green suite.

Completing only part of the work does not satisfy the invariant. Finishing one architectural layer, internal milestone, scaffold, adapter, endpoint, contract, migration, service, integration slice, test subset, or wiring step while other required issue-scoped or correction-scoped work remains is still incomplete work.

Compile errors, build failures, failing tests, unfinished wiring, unfinished contracts, unfinished tests, partial scaffolding, or another executable defect introduced by or within the current work are not terminal states when Agent #1 can continue diagnosing or correcting them.

If Agent #1 can identify the executable issue-scoped or correction-scoped work that remains, that is evidence that Agent #1 knows what to do next and should continue. Needing "one more continuation," being "not ready for review yet," or reporting that "the artifact still needs further implementation" does not justify returning control.

---

# Universal pre-return completion gate

Before Agent #1 returns control after any implementation or correction cycle, compare the current artifact against the governing issue scope, applicable review finding/correction scope, and acceptance criteria.

If required executable work remains incomplete and no material blocker exists, Agent #1 must continue execution instead of returning a progress report.

Agent #1 has only these valid terminal states while owning implementation or correction work:

## READY FOR AGENT #2 REVIEW

Use this state after initial implementation only when:

- all issue-scoped implementation is complete;
- required tests, contracts, migrations, configuration, wiring, and integration work are complete as applicable;
- relevant validation has been run;
- acceptance criteria have been checked against the current artifact;
- any issue-scoped or current-artifact build/test failures have been resolved;
- unrelated pre-existing failures, if any, are evidenced separately rather than absorbed into scope;
- the work remains uncommitted and unpushed;
- the artifact is ready for independent review.

## READY FOR AGENT #2 RE-REVIEW

Use this state after a correction cycle only when:

- the full executable correction required by the demonstrated blocker is complete;
- all correction-scoped wiring, contracts, migrations, adapters, services, tests, configuration, and related changes required for the corrected artifact to work are complete as applicable;
- relevant validation has been run;
- issue-scoped, correction-scoped, or current-artifact build/test failures have been resolved;
- unrelated pre-existing failures, if any, are evidenced separately rather than absorbed into scope;
- the corrected artifact is ready for independent re-review.

## MATERIAL BLOCKER

Use this state only when a concrete blocker prevents further safe execution and cannot be resolved from:

- the governing issue;
- the current repository;
- existing architecture and established repository patterns;
- established product or engineering decisions;
- authoritative external/provider documentation.

A material-blocker response must identify the exact missing decision, dependency, credential, integration boundary, authoritative answer, or other concrete requirement needed to continue.

The following are **not valid terminal states**:

- "implementation is still in progress";
- "not ready for Agent #2 review yet";
- "not ready for Agent #2 re-review yet";
- partial scaffolding is complete;
- a progress summary;
- a list of remaining implementation or correction tasks;
- reaching a natural internal milestone;
- finishing one file, layer, migration, service, or component while other required work remains;
- compile errors Agent #1 can continue fixing;
- failing tests Agent #1 can continue diagnosing or fixing;
- unfinished wiring;
- unfinished contracts;
- unfinished tests;
- needing "one more continuation";
- "the artifact still needs further implementation."

---

# Role separation

- **Agent #1 — Implementation Agent**
- **Agent #2 — Independent Review Agent**

Agent #1 must not satisfy Agent #2's review gate by spawning, simulating, impersonating, or internally controlling its own reviewer.

Agent #1 returns control for Agent #2 only after the universal pre-return completion gate produces `READY FOR AGENT #2 REVIEW` or `READY FOR AGENT #2 RE-REVIEW`, as appropriate.

The requirement is a real workflow handoff, not merely a different persona.

---
# Phase 1 — Agent #1 implementation

Agent #1:

1. reads the governing issue and acceptance criteria;
2. inspects current repository state and relevant existing architecture;
3. resolves material ambiguity before consequential implementation;
4. creates/uses the issue-specific branch from the designated integration branch;
5. implements only the issue scope;
6. reuses established owning abstractions where appropriate;
7. adds/updates tests and contracts needed for the changed behavior;
8. runs focused validation;
9. inspects the full working-tree diff;
10. reports implementation and repository state.

## Supabase table creation

When Agent #1 creates a Supabase table, do not assume that Data API access should exist and do not rely on historical automatic grants.

Use the access model established during readiness:

- if no Data API role should access the table, do not add Data API grants merely by convention;
- if Data API access is required, the creating migration must explicitly grant only the required operations to only the intended PostgreSQL roles;
- keep PostgreSQL grants distinct from RLS policies: grants control whether the role may perform the table operation at all, while RLS controls which rows are permitted;
- the migration must reconstruct the intended access model from scratch without depending on prior Supabase defaults;
- where relevant to the issue, validate reconstructed role-based access through the same path the application uses.

Do not broaden permissions for convenience.

## Mandatory hold point

Agent #1 must **not** commit, push, open a PR, or self-satisfy Agent #2 review.

This hold point is reached only after the universal pre-return completion gate produces `READY FOR AGENT #2 REVIEW`.

Agent #1 then returns control for separate Agent #2 review with a concise implementation summary and the artifact still uncommitted and unpushed.

---
# Phase 2 — Agent #2 review

Agent #2 uses `independent-implementation-review`.

The review decision is based only on:

- **Acceptance criteria met?**
- **New bug/regression introduced?**
- **Does it work?**

If:

```text
Yes -> No -> Yes
```

the implementation is approved.

Agent #2 may inspect surrounding code only as needed to answer those questions.

Agent #2 must not turn review into an open-ended architecture/application audit.

Suggestions do not change the approval decision.

---

# Phase 3 — Correction loop

If Agent #2 identifies a genuine current-implementation blocker:

1. control returns to Agent #1;
2. Agent #1 completes the full executable correction required by the demonstrated blocker;
3. Agent #1 completes all correction-scoped wiring, contracts, migrations, adapters, services, tests, configuration, and related changes required for the corrected artifact to work;
4. Agent #1 resolves build/test failures that are correction-scoped, issue-scoped, or introduced by the current artifact;
5. unrelated pre-existing failures are evidenced and reported separately without expanding correction scope;
6. Agent #1 runs relevant validation;
7. Agent #1 applies the universal pre-return completion gate;
8. only `READY FOR AGENT #2 RE-REVIEW` or `MATERIAL BLOCKER` may return control;
9. after `READY FOR AGENT #2 RE-REVIEW`, separate Agent #2 re-review answers the same three questions again.

Repeat only while one of the three governing answers fails.

Do not expand the correction into:

- preexisting bugs;
- architecture cleanup;
- future hardening;
- unrelated tests;
- refactoring;
- adjacent performance/security concerns;
- other suggestions not caused or materially worsened by the current implementation.

A correction is not complete merely because one part of the fix is implemented. If Agent #1 can identify remaining executable correction work and no material blocker prevents it, Agent #1 must continue rather than return a progress summary.

Non-blocking observations belong in the repository's consolidated `Application Improvement Suggestions` backlog, not in the current implementation.

---
# Phase 4 — Commit, push, and integration PR

After Agent #2 returns `APPROVED`, Agent #1 may:

1. verify the reviewed working-tree state has not changed;
2. stage only reviewed files;
3. commit;
4. push the issue branch;
5. open the integration PR.

The PR should accurately describe:

- governing issue;
- implemented behavior;
- validation performed;
- any lightweight suggestion references when relevant.

Do not turn the PR description into a catalog of unrelated application problems.

---

# Phase 5 — Integration PR review

Use `integration-pr-review`.

The intended PR flow is:

1. run one normal automated/Codex review on the stable implementation head;
2. independently evaluate each automated finding against the same three questions;
3. route findings that do not fail one of those questions to `Application Improvement Suggestions`;
4. correct only genuine current-PR blockers; every Agent #1 correction remains governed by the global Agent #1 execution invariant and universal pre-return completion gate;
5. use separate Agent #2 narrow correction re-review only after Agent #1 reaches `READY FOR AGENT #2 RE-REVIEW`;
6. if Agent #2 returns `APPROVED — MERGE`, commit/push the approved correction;
7. verify the pushed artifact matches what Agent #2 reviewed;
8. satisfy required CI/human approval;
9. merge.

**Do not automatically trigger another automated review merely because an approved correction changed the PR head.**

A second automated review is appropriate only when there is a specific artifact-mismatch concern or an explicit repository rule requires it.

The previous recursive final-head review behavior is not part of this workflow.

---

# Consolidated suggestion backlog

Each application repository should maintain one persistent issue named:

`Application Improvement Suggestions`

Non-blocking review observations should be appended there rather than generating a new GitHub issue per observation.

Each entry should contain:

- originating issue/PR;
- short description;
- why it may be worth considering;
- review-comment reference when available.

A suggestion does not imply severity, priority, production gating, commitment, or planned implementation.

Create a standalone issue only when the builder/workflow owner deliberately promotes a suggestion into planned work.

---

# Promotion

Promotion should remain intentionally boring.

That means:

- no planned feature implementation;
- no opportunistic refactoring;
- no acceptance-criteria completion;
- no intentional feature-development cycle inside promotion.

Review findings remain information, not commands.

Do not drag unrelated application improvements into promotion merely because they were noticed late.

If the release artifact itself contains a demonstrated defect that prevents approved behavior from working safely as intended, route the correction through the normal implementation/review workflow rather than implementing it inside the promotion PR.

---

# Zero gum on the bottom of the shoe

The phrase remains a release-discipline principle.

It means:

- do not knowingly promote a release artifact whose approved behavior is demonstrably broken;
- do not hide planned corrective implementation inside promotion;
- do not use promotion as another feature-development cycle.

It does not mean the application must have zero known suggestions or historical weaknesses before release.

---

# Prompt-generation requirement

Prompts generated from this workflow should stay small.

The prompt supplies the task; the skill supplies the procedure.

Do not restate the whole workflow in each prompt.

In particular:

- Agent #1 prompts preserve the no-commit/no-push/no-PR hold point;
- Agent #1 prompts do not need to repeat the global continuation invariant, universal pre-return completion gate, or valid terminal states; this skill owns those rules;
- Agent #1 inspects existing architecture before inventing new structures;
- material ambiguity is clarified instead of guessed through;
- Agent #2 prompts use the three-question review contract;
- correction prompts address only demonstrated current-implementation blockers;
- suggestions do not expand implementation scope;
- integration PR prompts invoke `integration-pr-review`;
- correction-finalization prompts do **not** restart automated review merely because the approved correction changed the head.

---

# Workflow summary

```text
Issue
  ↓
Agent #1 inspect architecture + clarify material ambiguity
  ↓
implement / validate issue scope
  ↓
universal pre-return completion gate
  ├── MATERIAL BLOCKER → return exact blocker → resume interrupted cycle after resolution
  └── READY FOR AGENT #2 REVIEW
             ↓
        separate Agent #2:
          Acceptance criteria met?
          New bug/regression introduced?
          Does it work?
             ↓
        Yes -> No -> Yes?
        ├── No → Agent #1 full correction
        │          ↓
        │       universal pre-return completion gate
        │          ├── MATERIAL BLOCKER
        │          └── READY FOR AGENT #2 RE-REVIEW
        │                     ↓
        │                separate Agent #2 re-review
        └── Yes
               ↓
            commit / push / integration PR
               ↓
            one normal automated PR review
               ↓
            evaluate findings against same three questions
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
                               verify artifact + CI/human approval
                                    ↓
                               MERGE
               ↓
            intentionally boring promotion
```

---
# Definition of workflow compliance

An implementation follows this skill only when:

- Agent #1 inspects existing architecture before creating new architecture;
- material ambiguity is clarified rather than guessed through;
- implementation remains inside issue scope;
- the Agent #1 execution invariant applies to initial implementation and every correction cycle;
- Agent #1 does not voluntarily return control while executable issue-scoped implementation or validated correction work remains and no material blocker exists;
- Agent #1 continues resolving build/test failures that are issue-scoped, correction-scoped, or introduced by the current artifact;
- unrelated pre-existing failures are evidenced and reported separately and do not force scope expansion merely to achieve a globally green suite;
- every Agent #1 implementation or correction cycle passes the universal pre-return completion gate;
- Agent #1 returns only at `READY FOR AGENT #2 REVIEW`, `READY FOR AGENT #2 RE-REVIEW`, or `MATERIAL BLOCKER`, as appropriate;
- a material blocker identifies the exact missing decision, dependency, integration boundary, credential, or authoritative answer required to continue;
- progress reporting, partial scaffolding, internal milestones, remaining-task lists, resolvable compile/build failures, and resolvable test failures are not treated as valid terminal states;
- after a material blocker is resolved, Agent #1 resumes the interrupted implementation or correction cycle;
- Agent #1 returns for independent Agent #2 review/re-review only after the applicable ready state is reached;
- Agent #2 is separately invoked;
- review is governed by the three questions;
- only acceptance failures, implementation-introduced regressions, or non-working behavior require correction;
- suggestions do not expand the current implementation;
- non-blocking observations route to `Application Improvement Suggestions`;
- automated findings are independently evaluated;
- correction re-review is narrow;
- `Yes -> No -> Yes` yields approval;
- integration PR corrections do not automatically restart automated review;
- required CI and human approval remain in force;
- promotion remains intentionally boring.
