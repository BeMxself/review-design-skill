---
name: review-design
description: Use when running or facilitating iterative design-document reviews with human+AI collaboration, especially when you need to extract review dimensions from prior review records, build a layered review plan, and adapt scope across rounds until the human decides to close.
---

# Review Design Workflow

## Overview

Turn scattered review notes into a reusable, iterative review workflow:

1. Analyze the design doc nature first.
2. Extract review dimensions from history (or current draft).
3. Build a layered and ordered review plan.
4. Run human+AI multi-round review with dynamic reprioritization.
5. Let the human explicitly decide when to close (never close only because round count is reached).

## Platform Neutrality

- Keep this workflow platform-agnostic.
- Do not require platform-specific invocation syntax in the workflow body (for example, `/skill-name` or `$skill-name`).
- If invocation guidance is needed, place it in platform docs (README), not in the skill logic.

---

## Dimension Extraction Baseline (from a multi-round case review evolution)

Use this as a default dimension pool, then tailor by document type.

1. **Architecture correctness**
  - Abstraction boundaries, flow ownership, responsibility split, protocol modeling.
2. **Security and abuse resistance**
  - Threats, token lifecycle, replay/open-redirect/injection/substitution risks, secret handling.
3. **Domain/data/process completeness**
  - Data model fields/constraints, full lifecycle flows, edge paths, error semantics.
4. **Consistency and contradiction**
  - Internal contradictions, naming drift, cross-section mismatch, invalid or self-negating design.
5. **Implementation alignment**
  - Claims vs existing code contracts, interface signatures, lifecycle assumptions, migration feasibility.
6. **Operability / NFR**
  - Deployment constraints, cache topology, timeout/health/logging/i18n/observability requirements.
7. **Document structure and readability**
  - Section ownership, duplication, navigation, scanability, decision placement.

If the target document is not security/integration heavy, do not force this exact priority; re-rank after archetype classification (see "Anti-Overfitting Guardrails").

Baseline order (high -> low): **1/2 -> 3/4 -> 5 -> 6 -> 7**

Rationale from the reviewed case:
- Early rounds found high-impact architecture/security flaws.
- Mid rounds focused on consistency/completeness.
- Later rounds focused on code alignment and precision.
- Structure/readability optimization was most effective after core semantics stabilized.

Do not treat this baseline as fixed phase gates.
In the observed review evolution, high-risk items reappeared in later rounds (for example, implementation-alignment and security precision issues after structure cleanup), so strict one-way progression is unsafe.

---

## Adaptive Ordering Engine (replace rigid round sequencing)

For each dimension, score 0-3 on:

- **Impact**: security/business correctness blast radius if wrong.
- **Rework cost**: redesign cost if discovered late.
- **Uncertainty**: how speculative or under-defined current design is.
- **Evidence gap**: mismatch risk versus requirements/code/contracts.

Priority score:

`score = 4*Impact + 3*ReworkCost + 2*Uncertainty + 1*EvidenceGap`

Round planning rule:
- Pick top 2 dimensions by score as primary focus.
- Pick 1 sentinel dimension for drift detection:
  - always include **Security** sentinel for auth/payment/data-sensitive designs.
  - include **Implementation alignment** sentinel whenever code or existing contracts exist.

This keeps flexibility while preventing late surprise regressions.

---

## Anti-Overfitting Guardrails

Before Round 1, run this check:

1. **Archetype first, not history first**
  - Classify document archetype: integration/security, domain-model, API contract, migration, operations, frontend UX, governance/policy.
2. **Dimension remap**
  - Keep only relevant dimensions; do not keep dimensions just because they appeared in prior projects.
3. **Sentinel remap**
  - Sentinel is not always security:
    - policy/governance docs -> compliance/consistency sentinel
    - ops docs -> reliability/operability sentinel
    - pure UX docs -> flow/usability sentinel
4. **Vocabulary drift check**
  - If findings repeatedly reuse old-project terminology without direct evidence in current doc, mark as potential overfit and re-review that item.
5. **Human calibration**
  - Ask the human whether the current priority profile feels domain-biased; adjust before continuing.

---

## Round 0: Document Typing and Initial Plan

Before reviewing details, classify the document:

- Stage: brainstorming / solution design / implementation design / migration plan / operations plan.
- Risk profile: security-critical, data-critical, external integration heavy, low risk.
- Change surface: new module / refactor / incremental enhancement.
- Validation source availability: code exists vs pure greenfield.

Then produce an initial review plan with:

- Chosen dimensions (subset from baseline).
- Priority tier per dimension (`P0`, `P1`, `P2`).
- Round objective sequence (draft, not fixed).
- Exit signals (what would make this review “ready to close”).

Also include dynamic ordering inputs:
- Dimension scores (`Impact/Rework/Uncertainty/EvidenceGap`).
- Selected sentinel dimension(s).
- Rebalance triggers (what conditions force priority reshuffle).

Important: explicitly ask the human to adjust this plan before Round 1.

---

## Iterative Review Loop (Round N)

For each round:

1. **Set focus**
  - Select 1-3 dimensions as this round focus.
  - Keep at least one unresolved high-risk dimension in scope.

2. **Review and emit findings**
  - Use issue cards with: `ID`, `dimension`, `severity`, `evidence`, `impact`, `suggested options`.
  - Separate facts from suggestions.

3. **Human decision checkpoint**
  - For each issue: `accept / reject / defer / partial`.
  - Record rationale (one sentence is enough).

4. **Plan update**
  - Re-score open issues and reprioritize dimensions.
  - Add/remove/merge next-round focus items.

5. **Round summary**
  - New issues, resolved issues, residual high-risk items, plan delta.

Do not force linear progression if new blocker appears; jump back to higher-priority dimensions when needed.

Rebalance triggers (recommended):
- Any newly discovered `S0/S1` outside current focus -> immediate next-round top priority.
- If contradiction-type findings dominate (>30%), schedule a consistency-focused round.
- If evidence disputes dominate (design claims vs code/contract mismatch), pull implementation-alignment forward.

---

## Severity and Priority Rules

Use stable severity labels:
- `S0`: release/safety/business/regulatory blocker
- `S1`: major correctness/completeness risk
- `S2`: important precision/consistency gap
- `S3`: readability/maintainability optimization

Prioritization rules:
- Any open `S0` keeps review in “continue” mode by default.
- `S1` can close only with explicit human risk acceptance.
- `S2/S3` can be batched or deferred with tracked TODO.
- If a low-layer round finds an `S0/S1`, immediately lift that dimension to next round top priority.

Anti-pattern to avoid:
- “Round count done, so close review.”
- “Already entered readability phase, so do not revisit architecture/security.”

---

## Human-AI Collaboration Contract

AI responsibilities:
- Propose structure, surface risks, maintain issue ledger, suggest concrete options.
- Keep plan mutable and transparent each round.

Human responsibilities:
- Decide acceptance and trade-offs.
- Approve priority changes when needed.
- Decide whether to continue or close review.

AI must not auto-declare review complete without explicit human closure decision.

---

## Round Output Template

Use this concise structure each round:

```markdown
## Round N Plan
- Focus dimensions:
- Why these first:
- Human-adjustable changes:

## Findings
### RN-X [dimension][severity] title
- Evidence:
- Risk:
- Options:
- Recommendation:

## Decision Log (Human)
- RN-X: accept/reject/defer/partial (+ rationale)

## Next Round Draft
- Carry-over risks:
- Proposed focus:
- Plan changes:
```

---

## Closure Protocol (Human-Gated)

At the end of each round, ask for explicit status:

- `继续审查` (continue with updated plan)
- `有条件收口` (close with accepted residual risks/TODOs)
- `结束审查` (ready for implementation)

Before closure, provide:
- Open issue ledger by severity.
- Explicit residual risk statement.
- Implementation watchlist derived from deferred items.

Only close after human confirmation.

---

## Practical Notes

- Keep this workflow flexible: dimensions and order are defaults, not hard rules.
- When review history exists, extract dimensions from that history first, then refine.
- Prefer “small rounds + explicit decisions” over one giant review dump.
- Preserve traceability: every accepted/deferred item should be linkable to a specific issue ID.
- Prefer dual-track execution: primary focus dimensions + one sentinel dimension every round.
