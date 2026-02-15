---
name: review-design
description: Use when running multi-round human+AI design-document reviews that require issue tracking, versioned revisions, and explicit human-gated closure.
---

# Review Design Workflow

## Core Contract

This skill is for iterative review of evolving design docs, not one-shot critique.

Mandatory loop:
1. Review current design version `Dn`.
2. Save round report `Rn` (findings + status snapshot).
3. Human+AI disposition each issue.
4. Revise design to `Dn+1` with issue-to-change log.
5. Human chooses next step: `继续审查 / 有条件收口 / 结束审查`.

Hard rules:
- One round per response.
- Never auto-advance without explicit human decision.
- Never run Round `N+1` on unchanged design.
- Every issue must have a handling record.

## Round 0 (Setup)

Classify context:
- Stage: brainstorming / solution / implementation / migration / operations.
- Risk profile: security-critical / data-critical / integration-heavy / low-risk.
- Change surface: new module / refactor / incremental enhancement.
- Validation source: existing code/contracts vs greenfield.

Define review state:
- Design baseline: `D0`.
- Report baseline: `R0`.
- Stable issue IDs (for example `R1-1`, `R1-2`).
- Issue status: `open / resolved / deferred / accepted-risk`.

Pick dimensions and priority tier (`P0/P1/P2`):
1. Architecture correctness
2. Security and abuse resistance
3. Domain/data/process completeness
4. Consistency and contradiction
5. Implementation alignment
6. Operability/NFR
7. Structure/readability

For refactor/redesign touching existing capabilities, declare intent:
- `replace`
- `coexist`
- `bypass`
- `experiment`

Prepare duplication checkpoint:
- Existing capability inventory (interface/class/module + responsibility).
- Overlap map (duplicate / extend / replace).
- Justification evidence (why existing implementation is insufficient).
- Coexistence and sunset plan (owner, trigger, timeline, rollback).

Ask the human to confirm or adjust this plan before Round 1.

## Anti-Overfitting Guardrails

- Classify document archetype first; do not copy old-project priorities blindly.
- Keep only dimensions relevant to current doc type.
- If findings use legacy vocabulary without evidence, re-check for overfitting.
- Ask the human whether priority profile looks domain-biased before continuing.

## Prioritization Engine

Score each dimension (0-3):
- `Impact`
- `ReworkCost`
- `Uncertainty`
- `EvidenceGap`

Priority formula:
`score = 4*Impact + 3*ReworkCost + 2*Uncertainty + 1*EvidenceGap`

Round focus rule:
- Pick top 2 dimensions by score.
- Add 1 sentinel:
  - Security sentinel for auth/payment/sensitive-data designs.
  - Implementation-alignment sentinel when existing code/contracts exist.

## Round N Protocol

### Phase A: Review `Dn`

- Review 1-3 focus dimensions.
- Emit issue cards with:
  - `ID`, `dimension`, `severity`, `evidence`, `impact`, `options`, `recommendation`
- For duplication-related issues also include:
  - `intent`, `replacement_scope`, `coexistence_window`, `sunset_plan`

### Phase B: Persist `Rn` (Mandatory)

Save a round report before moving to dispositions. Report must include:
- Input design version (`Dn`)
- Findings and severity
- Issue status snapshot
- Carry-over high-risk items

### Phase C: Issue-by-Issue Human+AI Disposition (Mandatory)

For each issue, record:
- Decision: `accept / reject / defer / partial`
- Rationale
- Handling action (what to change, or explicit no-change reason)

No issue should be left without explicit handling notes.

### Phase D: Revision (Mandatory Before Next Round)

- Apply agreed actions to produce revised design `Dn+1`.
- Produce change log: `Issue ID -> design section delta`.
- If `Dn+1` is not ready, stay in disposition/revision mode.
- Do not start Round `N+1` until revised design exists.

### Phase E: Round Summary + Human Gate (Mandatory)

Summarize:
- New issues
- Resolved issues
- Residual high-risk items
- Plan delta

Then ask explicit decision:
- `继续审查`
- `有条件收口`
- `结束审查`

Do not continue to the next round in the same response.

## Intent-Aware Duplication Rules

Duplication is not automatically a defect.

Intentional duplication (acceptable with controls):
- Intent declared (`replace/coexist/bypass/experiment`)
- Evidence that existing capability is insufficient
- Bounded coexistence window
- Clear sunset and rollback plan

Accidental duplication (issue):
- No intent declaration
- Re-implements existing responsibilities without evidence
- Creates parallel ownership or conflicting semantics

Severity guidance:
- `S1`: core-flow correctness/security/ownership ambiguity
- `S2`: localized complexity/maintainability burden

Decision logging labels:
- `accept duplication`
- `defer pending evidence`
- `reject as accidental duplication`

## Rebalance Triggers

Reprioritize immediately when:
- A new `S0/S1` appears outside current focus.
- Contradiction findings exceed about 30%.
- Evidence disputes (design vs code/contract) dominate.
- Duplication intent is unclear or sunset path is missing.

## Severity and Closure Rules

Severity:
- `S0`: release/safety/business/regulatory blocker
- `S1`: major correctness/completeness risk
- `S2`: important precision/consistency gap
- `S3`: readability/maintainability optimization

Closure defaults:
- Any open `S0` => continue by default.
- `S1` can close only with explicit human risk acceptance.
- `S2/S3` can be deferred with tracked TODOs.

## Round Output Template

```markdown
## Round N Plan
- Input design version: Dn
- Focus dimensions:
- Why now:
- Human-adjustable changes:

## Findings
### RN-X [dimension][severity] title
- Evidence:
- Impact:
- Options:
- Recommendation:
- Duplication intent (if relevant):

## Saved Report
- Report ID: Rn
- Issue status snapshot:

## Decision Log (Human)
- RN-X: accept/reject/defer/partial (+ rationale)

## Revision Actions
- RN-X -> expected design change / explicit no-change reason

## Revised Design Checkpoint
- Revised design version: Dn+1 (or `not ready`)
- Change log (Issue ID -> section delta):

## Next Round Draft
- Carry-over risks:
- Proposed focus:
- Plan changes:

## Human Gate
- 继续审查 / 有条件收口 / 结束审查
```

## Closure Protocol (Human-Gated)

Before closure, provide:
- Open issue ledger by severity
- Residual risk statement
- Implementation watchlist from deferred items

Never close without explicit human confirmation.
