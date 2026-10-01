---
name: application-implementation-workflow
description: Shorthand: implement-workflow. Govern the application implementation lifecycle with scoped Agent #1 implementation, independent Agent #2 review, coherent issue-bound corrections, one normal automated PR review plus a bounded final-head artifact-integrity review after validated corrections, and boring promotion. Inspect existing architecture before inventing new structures, ask when material requirements are ambiguous, and use the three-question review contract without expanding work into unrelated application auditing.
---

# Application Implementation Workflow

## Executing agent identity

**You are Agent #1 — Implementation Agent.**

This skill invocation establishes your agent identity for the entire execution.

**A skill invocation has one agent identity. That identity remains fixed for the entire execution. The executing agent may not change roles during the invocation.**

As Agent #1, you own:

- implementation;
- correction;
- implementation validation;
- preparing artifacts for independent review;
- PR creation/finalization and other implementation-side production-bound work assigned to Agent #1.

You may write only Agent #1 workflow-state markers:

- `[AGENT #1 — IMPLEMENTATION]`
- `[AGENT #1 — CORRECTION]`

You must **not**:

- perform Agent #2's independent review;
- disposition findings on Agent #2's behalf;
- create `[AGENT #2 — INDEPENDENT REVIEW]`;
- approve your own implementation as though Agent #2 approved it;
- treat your own inspection, testing, or validation as independent review;
- spawn, simulate, impersonate, or internally assume Agent #2.

**Reaching another agent's workflow responsibility is a handoff boundary, not an instruction for the current agent to continue by assuming that responsibility.**

A transition from Agent #1 to Agent #2 requires a separate agent invocation.

## Purpose

This skill defines the canonical implementation lifecycle for application software constructed with AI agents.

It keeps implementation scoped to the governing issue while preserving independent review and practical delivery throughput.

## Invocation model

The governing Issue/PR is the authoritative source of change-specific requirements, acceptance criteria, scope, constraints, and durable workflow history/state. The current repository/artifact remains authoritative for what actually exists.

This skill owns the reusable implementation procedure. The invocation prompt does not need to restate issue content, prior execution history, or workflow rules already recorded in GitHub and defined here.

When the agent is already operating in the correct repository workspace, a one-line invocation is sufficient, for example:

```text
Implement FE #1510 using application-implementation-workflow
```

For a correction:

```text
Correct FE #1510 using application-implementation-workflow
```

Resolve the requested skill, retrieve/read the governing Issue or PR, identify the latest relevant workflow-state comment for the current phase, inspect the current artifact, and execute this workflow. Do not require the prompt to duplicate the issue specification, prior Agent #1/Agent #2 state, branch rules, review rules, or recurring `DO NOT` instructions owned by this skill.

Do not rely on the surrounding ChatGPT conversation as the durable workflow record.

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

# Durable workflow-state comments

GitHub Issues and PRs are the durable record of implementation/review execution state.

Use recognizable workflow-state markers so agents can identify the latest relevant execution state without assuming the absolute newest comment is relevant. Human comments, Codex comments, inline review comments, and other activity may appear after a workflow-state comment.

These markers are owned by Agent #1. Agent #1 must never create an Agent #2 workflow-state comment.

Agent #1 uses:

```text
[AGENT #1 — IMPLEMENTATION]
```

for the initial implementation result, and:

```text
[AGENT #1 — CORRECTION]
```

for correction results.

Before acting, Agent #1 must:

1. inspect the current Issue or PR;
2. identify the latest relevant workflow-state comment for the current phase;
3. for a correction, identify the latest applicable `[AGENT #2 — INDEPENDENT REVIEW]` disposition;
4. use that history as execution context together with the current artifact and governing issue/PR;
5. perform the implementation/correction defined by this skill;
6. post the result back to the same Issue or PR as a new structured workflow-state comment.

Workflow-state comments are historical/execution records, not substitutes for inspecting the current artifact. If a comment and the current repository state disagree, the current artifact is authoritative.

Agent #1 comments must be concise but sufficient for the next invocation to continue without reconstructing context from ChatGPT history.

For initial implementation, use:

```markdown
[AGENT #1 — IMPLEMENTATION]

Status: READY FOR AGENT #2 REVIEW | MATERIAL BLOCKER
Artifact/Branch: <current artifact or branch reference>
Implemented:
- <concise issue-scoped summary>
Validation:
- <checks run and result>
Environment / Deployment Requirements:
- <requirements>
—or—
Environment / Deployment Requirements: None
Notes:
- <material blocker or concise relevant state, if any>
```

For correction, use:

```markdown
[AGENT #1 — CORRECTION]

Status: READY FOR AGENT #2 RE-REVIEW | MATERIAL BLOCKER
Disposition Addressed: <latest applicable Agent #2 review comment/reference>
Artifact/Branch: <current artifact or branch reference>
Corrected:
- <coherent issue-scoped correction summary>
Validation:
- <checks run and result>
Environment / Deployment Requirements:
- <updated requirements>
—or—
Environment / Deployment Requirements: None
Notes:
- <material blocker or concise relevant state, if any>
```

Do not reproduce the entire prompt or skill in these comments.

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
- the authoritative Environment / Deployment Requirements have been surfaced, including `None` when no actions are required;
- the artifact is ready for independent review.

## READY FOR AGENT #2 RE-REVIEW

Use this state after a correction cycle only when:

- the full executable correction required by the demonstrated blocker is complete at its coherent issue-scoped behavior boundary;
- directly affected sibling states/transitions governed by the same demonstrated root cause have been corrected when necessary for the original issue-scoped behavior to work;
- all correction-scoped wiring, contracts, migrations, adapters, services, tests, configuration, and related changes required for the corrected artifact to work are complete as applicable;
- relevant validation has been run;
- issue-scoped, correction-scoped, or current-artifact build/test failures have been resolved;
- unrelated pre-existing failures, if any, are evidenced separately rather than absorbed into scope;
- any Environment / Deployment Requirements introduced or changed by the correction have been incorporated into the authoritative list;
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

Agent #1 and Agent #2 are distinct agents, not personas, modes, phases, or interchangeable responsibilities of one agent.

This skill is an Agent #1 skill. Agent #1 must not satisfy Agent #2's review gate by spawning, simulating, impersonating, internally controlling, or otherwise assuming its own reviewer.

Agent #1 returns control for Agent #2 only after the universal pre-return completion gate produces `READY FOR AGENT #2 REVIEW` or `READY FOR AGENT #2 RE-REVIEW`, as appropriate.

At either state, Agent #1 must stop. A separately invoked Agent #2 must perform the independent review.

The requirement is a real workflow handoff, not merely a different persona.

---
# Phase 1 — Agent #1 implementation

Agent #1:

1. reads the governing Issue/PR, acceptance criteria, and relevant prior workflow-state comments;
2. identifies the latest applicable workflow-state comment for the current phase without assuming the newest GitHub comment is relevant;
3. inspects current repository state and relevant existing architecture;
4. resolves material ambiguity before consequential implementation;
5. creates/uses the issue-specific branch from the designated integration branch;
6. implements only the issue scope;
7. reuses established owning abstractions where appropriate;
8. adds/updates tests and contracts needed for the changed behavior;
9. runs focused validation;
10. inspects the full working-tree diff;
11. identifies and maintains the authoritative Environment / Deployment Requirements for the current artifact;
12. posts the structured `[AGENT #1 — IMPLEMENTATION]` workflow-state comment to the governing Issue or PR with implementation, validation, environment/deployment requirements, and terminal status.

## Supabase table creation

When Agent #1 creates a Supabase table, do not assume that Data API access should exist and do not rely on historical automatic grants.

Use the access model established during readiness:

- if no Data API role should access the table, do not add Data API grants merely by convention;
- if Data API access is required, the creating migration must explicitly grant only the required operations to only the intended PostgreSQL roles;
- keep PostgreSQL grants distinct from RLS policies: grants control whether the role may perform the table operation at all, while RLS controls which rows are permitted;
- the migration must reconstruct the intended access model from scratch without depending on prior Supabase defaults;
- where relevant to the issue, validate reconstructed role-based access through the same path the application uses.

Do not broaden permissions for convenience.

## Environment / Deployment Requirements

As implementation progresses, Agent #1 must identify and maintain the authoritative non-code actions required for the reviewed artifact to function in the target environment.

Evaluate at minimum, when applicable:

- SQL migrations or database schema/data changes that must be executed;
- manual database operations;
- environment variables;
- secrets;
- third-party/provider configuration;
- webhook configuration;
- external resource creation such as provider products, prices, queues, buckets, topics, or similar resources;
- seed data or UAT fixture requirements;
- infrastructure/platform configuration;
- restart or redeploy requirements;
- post-deployment verification requirements.

These requirements must be surfaced before the implementation is handed forward for merge orchestration.

If no environment/deployment actions are required, report exactly:

```text
Environment / Deployment Requirements: None
```

Do not omit the section.

A required environment action is not automatically a code defect. For example, an unapplied migration, missing staging secret, unconfigured webhook, absent staging/UAT fixture, or pending restart/redeploy is an environment/deployment requirement unless the implementation itself failed to provide a required migration, configuration contract, provider integration artifact, or other implementation deliverable needed for the feature to function.

Agent #1 owns keeping this list current. Readiness may identify anticipated requirements, but implementation must update the authoritative list as the actual artifact evolves.

## Mandatory hold point

Agent #1 must **not** commit, push, open a PR, or self-satisfy Agent #2 review.

This hold point is reached only after the universal pre-return completion gate produces `READY FOR AGENT #2 REVIEW`.

Agent #1 posts the structured `[AGENT #1 — IMPLEMENTATION]` comment, then returns control for separate Agent #2 review with the artifact still uncommitted and unpushed. That GitHub comment is the durable implementation handoff.

---
# Phase 2 — Agent #2 review boundary

This section describes the next workflow stage; it is **not executable by the current Agent #1 invocation**.

When Agent #1 reaches `READY FOR AGENT #2 REVIEW`, Agent #1 stops.

A separately invoked Agent #2 uses `independent-implementation-review`.

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
2. Agent #1 reads the governing Issue/PR and latest applicable `[AGENT #2 — INDEPENDENT REVIEW]` disposition, while also inspecting the complete current artifact;
3. Agent #1 corrects the demonstrated cause at its coherent issue-scoped behavior boundary, not merely the single reported permutation;
4. when the validated defect shows that the same issue-scoped state, transition, authority rule, recovery rule, or other behavior model governs directly related cases, Agent #1 corrects the directly affected sibling states/transitions necessary for the original issue-scoped behavior to work;
5. Agent #1 completes all correction-scoped wiring, contracts, migrations, adapters, services, tests, configuration, and related changes required for the corrected artifact to work;
6. Agent #1 resolves build/test failures that are correction-scoped, issue-scoped, or introduced by the current artifact;
7. unrelated pre-existing failures are evidenced and reported separately without expanding correction scope;
8. Agent #1 runs relevant validation;
9. Agent #1 applies the universal pre-return completion gate;
10. Agent #1 posts a new `[AGENT #1 — CORRECTION]` workflow-state comment to the same Issue/PR;
11. only `READY FOR AGENT #2 RE-REVIEW` or `MATERIAL BLOCKER` may return control;
12. after `READY FOR AGENT #2 RE-REVIEW`, Agent #1 stops; a separately invoked Agent #2 performs the re-review and answers the same three questions again.

## Coherent correction boundary

**Correct the demonstrated cause, not only the reported permutation.**

A validated blocker may reveal that one issue-scoped behavior rule governs multiple directly related states or transitions. In that case, the correction boundary includes the directly affected behavior family necessary to resolve the demonstrated defect coherently.

This does **not** authorize:

- unrelated refactoring;
- architecture cleanup;
- speculative hardening;
- neighboring product work;
- unrelated tests;
- backlog expansion;
- unrelated performance/security work;
- other suggestions not caused or materially worsened by the current implementation.

Diagnostic state models, scenario matrices, reviewer-generated examples, and hardening ideas do **not** become new acceptance criteria. They are evidence used to understand the demonstrated defect and correction boundary.

The governing questions remain:

1. Are the **original issue acceptance criteria** met?
2. Has the current artifact introduced or materially worsened a bug/regression?
3. Does the implemented behavior work?

A diagnostic scenario or reviewer-generated example requires correction only when it is required by the original issue acceptance criteria or demonstrates an actual defect/regression in the current artifact.

## Correction-churn trigger

Do not perform bounded state/transition analysis as mandatory ceremony for an ordinary first-pass correction.

Trigger it only when:

```text
initial blocker
  ↓
Agent #1 correction
  ↓
Agent #2 re-review finds another directly related failure
in the same behavioral subsystem
```

When that occurs, do not continue one-finding-at-a-time patching. Before the next correction:

1. perform a bounded issue-scoped model/state-transition analysis of that subsystem;
2. identify the coherent authority/state/transition rules that govern the demonstrated failures;
3. identify all currently demonstrable blockers within the original issue acceptance criteria, changed behavior, and demonstrated regression surface;
4. establish one coherent correction boundary;
5. return that bounded correction set to Agent #1.

The churn analysis remains diagnostic. It must not create new acceptance criteria, require exhaustive combinatorial testing, or expand into unrelated application archaeology.

Repeat the correction/re-review cycle only while one of the three governing answers fails.

A correction is not complete merely because one reported example was patched. If Agent #1 can identify remaining executable correction work inside the coherent issue-scoped boundary and no material blocker prevents it, Agent #1 must continue rather than return a progress summary.

Non-blocking observations belong in the repository's consolidated `Application Improvement Suggestions` backlog, not in the current implementation.

---

# Phase 4 — Commit, push, and integration PR

After Agent #2 returns `APPROVED`, Agent #1 may:

1. verify the reviewed working-tree state has not changed;
2. stage only reviewed files;
3. commit;
4. push the issue branch;
5. open the integration PR;
6. post the current `[AGENT #1 — IMPLEMENTATION]` workflow-state comment on the PR so production-bound review state is durable on the PR itself.

The PR should accurately describe:

- governing issue;
- implemented behavior;
- validation performed;
- `Environment / Deployment Requirements`, explicitly listing all known non-code actions or `None`;
- any lightweight suggestion references when relevant.

Do not turn the PR description into a catalog of unrelated application problems.

---

# Phase 5 — Integration PR review

Use `integration-pr-review`.

The intended production-bound PR flow is:

1. run one normal automated/Codex review on the stable initial implementation head;
2. independently evaluate each automated finding against the same three questions;
3. route findings that do not fail one of those questions to `Application Improvement Suggestions`;
4. correct only validated current-PR blockers; every Agent #1 correction remains governed by the global Agent #1 execution invariant, coherent correction boundary, churn trigger when applicable, and universal pre-return completion gate;
5. use separate Agent #2 narrow correction re-review only after Agent #1 reaches `READY FOR AGENT #2 RE-REVIEW`;
6. if Agent #2 returns `APPROVED — MERGE`, Agent #1 finalizes the approved correction as one continuous action: verify the approved artifact, commit, push exactly that approved correction, confirm the stable PR head, post the complete canonical Codex Integration Review Request defined by `integration-pr-review`, then stop;
7. verify the pushed artifact matches what Agent #2 reviewed;
8. because the production-bound PR head changed, run one bounded final-head automated/Codex review on that new stable head;
9. independently disposition every final-head finding against the same three governing questions;
10. if no validated blocker remains, satisfy required CI/human approval and merge.

The final-head automated review is an **artifact-integrity gate**. Automated review remains evidence, not authority.

Diagnostic scenarios, reviewer-generated matrices, and automated examples do not create new acceptance criteria. A final-head finding blocks only when it demonstrates:

1. an original acceptance criterion is unmet;
2. the current artifact introduced or materially worsened a bug/regression; or
3. the implemented behavior does not work.

## Final-head anti-recursion rule

A final-head automated review is **required** after a validated blocker correction changes the pushed production-bound PR head.

Do **not** run repeated automated reviews when no subsequent implementation correction changes the artifact.

Do **not** blindly obey automated findings or allow automated review to expand acceptance criteria.

If the final-head automated review identifies another validated blocker and another correction materially changes the artifact, the normal sequence applies again because the production-bound artifact changed:

```text
validated blocker
  ↓
Agent #1 coherent correction
  ↓
Agent #2 complete-artifact re-review
  ↓
APPROVED — MERGE
  ↓
commit + push exactly approved correction
  ↓
one bounded final-head automated review on new stable head
```

This is artifact convergence, not recursive review for its own sake.

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

The Issue/PR supplies the what and durable workflow history/state; the skill supplies the how; the prompt supplies only the action/location.

Do not restate the whole workflow in each prompt.

Small means concise and non-duplicative, not incomplete.

## Whole-artifact operational handoff

When producing an operational artifact intended for the orchestrating developer to copy/paste or execute, provide the **complete current authoritative artifact**.

This applies to:

- agent prompts;
- review/correction prompts;
- SQL scripts;
- migration execution scripts;
- shell/CLI command sequences;
- configuration blocks;
- deployment instructions intended for direct execution.

If an operational artifact changes, provide a new complete version. Do not require the orchestrating developer to reconstruct the authoritative artifact from earlier messages or combine fragments such as "replace this section," "append this," "keep everything else from the previous version," or "change these lines."

A diff, patch, changed section, or comparison is appropriate only when the developer explicitly asks for one.

This rule does not prohibit normal code diffs used during engineering review. It governs copy/paste operational handoffs to the orchestrating developer.

In particular:

- Agent #1 prompts preserve the no-commit/no-push/no-PR hold point;
- Agent #1 prompts do not need to repeat the global continuation invariant, universal pre-return completion gate, or valid terminal states; this skill owns those rules;
- Agent #1 inspects existing architecture before inventing new structures;
- material ambiguity is clarified instead of guessed through;
- Agent #2 prompts use the three-question review contract;
- correction prompts address only demonstrated current-implementation blockers;
- suggestions do not expand implementation scope;
- integration PR prompts invoke `integration-pr-review`;
- correction-finalization prompts require one bounded final-head automated review after an approved validated-blocker correction changes the pushed production-bound head; that review must use the complete canonical Codex Integration Review Request from `integration-pr-review`, not a bare trigger, and must not repeat when no subsequent implementation correction changes the artifact.

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
        separate Agent #2 complete-artifact review
             ↓
        blocker exposes shared behavior rule?
             ├── Yes → bounded sibling-state/transition sweep
             └── No
             ↓
        report all presently demonstrable issue-scoped blockers together
             ↓
        Yes -> No -> Yes?
        ├── No → Agent #1 coherent correction boundary
        │          ↓
        │       universal pre-return completion gate
        │          ├── MATERIAL BLOCKER
        │          └── READY FOR AGENT #2 RE-REVIEW
        │                     ↓
        │                separate Agent #2 complete-artifact re-review
        │                     ↓
        │                directly related sibling failure after correction?
        │                     ├── Yes → bounded churn analysis → one coherent correction boundary
        │                     └── No
        └── Yes
               ↓
            commit / push / integration PR
               ↓
            one normal automated PR review
               ↓
            independently disposition findings
               ↓
            validated current-PR blocker?
              ├── No → merge after required CI/human approval
              └── Yes → Agent #1 coherent correction
                         ↓
                      Agent #2 complete-artifact re-review
                         ↓
                      Yes -> No -> Yes = APPROVED — MERGE
                         ↓
                      commit / push exactly approved correction
                         ↓
                      one bounded final-head automated review
                         ↓
                      independently disposition findings
                         ↓
                      another validated blocker causing another correction?
                         ├── Yes → repeat correction / re-review / final-head sequence
                         └── No → CI / human approval → MERGE
               ↓
            intentionally boring promotion
```

---

# Definition of workflow compliance

An implementation follows this skill only when:

- the skill immediately establishes the executing agent as Agent #1 for the full invocation;
- Agent #1 never changes identity or assumes Agent #2 responsibilities during the invocation;
- reaching Agent #2 review/re-review is a hard stop and separate-invocation boundary;
- Agent #1 never creates `[AGENT #2 — INDEPENDENT REVIEW]` or self-approves independent review;
- Agent #1 reads the governing Issue/PR and latest relevant workflow-state comments before acting;
- Agent #1 records implementation/correction execution state back to the same Issue/PR using the structured workflow-state markers;
- GitHub workflow-state comments provide durable history but never replace inspection of the current artifact;
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
- the authoritative Environment / Deployment Requirements are maintained and surfaced before handoff, with `None` stated explicitly when no actions are required;
- copy/paste operational handoffs are delivered as complete current authoritative artifacts unless a diff/patch/changed section is explicitly requested;
- Agent #1 returns for independent Agent #2 review/re-review only after the applicable ready state is reached;
- Agent #2 is separately invoked;
- review is governed by the three questions;
- only original-acceptance failures, implementation-introduced regressions, or non-working behavior require correction;
- validated corrections address the coherent issue-scoped behavior boundary rather than only the reported permutation;
- diagnostic models, scenario matrices, reviewer-generated examples, and hardening ideas do not become new acceptance criteria;
- repeated directly related sibling failures in the same subsystem trigger bounded churn analysis before another correction;
- suggestions do not expand the current implementation;
- non-blocking observations route to `Application Improvement Suggestions`;
- automated findings are independently evaluated as evidence, not authority;
- correction re-review evaluates the complete current artifact;
- `Yes -> No -> Yes` yields approval;
- a validated blocker correction that changes the pushed production-bound PR head requires one bounded final-head automated review;
- repeated automated review without another implementation correction changing the artifact is not required;
- required CI and human approval remain in force;
- promotion remains intentionally boring.
