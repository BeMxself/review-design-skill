# review-design-skill

[中文](README.zh-CN.md)

A reusable design-document review skill for iterative human+AI collaboration.

## What this plugin provides

- A reusable skill: `design-review-workflow`
- Dynamic review planning (not rigid round order)
- Anti-overfitting guardrails (avoid domain lock-in)
- Human-gated closure protocol

## Repository layout

```text
skills/
  design-review-workflow/
    SKILL.md
    agents/openai.yaml
    references/
      external-auth-review-dimension-map.md
```

## Install

### Claude Code (use `/plugin`)

Install this repository via Claude Code's `/plugin` command, then enable `design-review-workflow`.

### Codex

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
ln -s /absolute/path/to/review-design-skill/skills/design-review-workflow "${CODEX_HOME:-$HOME/.codex}/skills/design-review-workflow"
```

### kiro-cli

```bash
mkdir -p ~/.kiro/skills
ln -s /absolute/path/to/review-design-skill/skills/design-review-workflow ~/.kiro/skills/design-review-workflow
```

## Use

Use `design-review-workflow` when reviewing design documents.

Platform invocation syntax is different, but the skill content should stay platform-agnostic:
- Claude Code: `/design-review-workflow`
- Codex: `$design-review-workflow`

Typical flow:
1. Round 0: classify document archetype + build initial review plan
2. Round N: iterative findings + decision log + plan rebalance
3. Close only after explicit human decision

## Notes

- The reference map is anonymized and does not expose source project details.
- This plugin is intended to be adapted for different design domains.
