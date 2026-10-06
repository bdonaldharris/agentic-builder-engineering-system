# Visible UI Metadata and Mechanism-Specific Evidence

**Date:** 2026-10-06  
**Status:** Evidence-backed candidate standards  
**Candidate ABES layers:** Product completion, agent context design, verification, review

## Finding

AI-generated interfaces can contain developer-style annotations, placeholder copy, design-showcase labels, mock data, or debugging residue. These artifacts must not be rationalized as intentional “agent breadcrumbs” without direct evidence of a specific tool's behavior.

A model does not gain a special memory channel merely because a string is rendered in customer UI. If the model can access the source, then the same identifier can reside in a component name, comment, test hook, structured metadata, or tool output without being shown to customers. If it cannot access the source or rendered DOM, displaying the string does not make it available to the model.

## Candidate standard: separate collaboration context from customer UI

Agent-readable collaboration context belongs in explicit, non-customer-facing channels: repository structure, component names, code comments, design-system metadata, project instructions, test identifiers, structured files, and authorized tool context.

Customer UI must be reviewed as product content. Before production release, audit visible strings, labels, badges, tooltips, empty states, errors, controls, and diagnostic surfaces. Remove, replace, restrict, or translate artifacts that do not help the intended customer understand, decide, or act.

This standard does not authorize indiscriminate removal. Preserve customer-relevant status, accessibility labels, useful support diagnostics, and intended error handling.

## Candidate standard: verify the mechanism, not the topic

Citations support a claim only when they support the claim's specific mechanism. A source that discusses context windows, delimiters, design-system metadata, or automated cleanup does not establish that an agent deliberately renders metadata in a customer UI to aid its own memory.

For material claims about agent behavior, reviewers should:

1. state the precise causal claim;
2. identify the claimed system or tool boundary;
3. request the exact supporting quotation or primary documentation;
4. compare the quotation to the claimed mechanism; and
5. record uncertainty rather than inferring certainty from topical similarity.

This is a concrete application of evidence-bounded claims: adjacent sources are not proof.

## Verification implications

- Inspect rendered UI in customer-representative states, not only source text.
- Use automated scans to surface likely residue, then make a human product judgment.
- Where a generated explanation drives workflow or policy, request direct evidence before codifying it.
- Treat “not documented” and “not verifiable from the available evidence” as valid results.
