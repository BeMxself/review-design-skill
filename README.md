# review-design-skill

A Claude Code skill plugin for iterative design-document review with human+AI collaboration.

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

### Option 1: copy skill folder

Copy `skills/design-review-workflow` into your Claude Code skills directory.

### Option 2: symlink for local development

```bash
mkdir -p ~/.claude/skills
ln -s /absolute/path/to/review-design-skill/skills/design-review-workflow ~/.claude/skills/design-review-workflow
```

## Use

Ask Claude Code to use `design-review-workflow` when reviewing design documents.

Typical flow:
1. Round 0: classify document archetype + build initial review plan
2. Round N: iterative findings + decision log + plan rebalance
3. Close only after explicit human decision

## Notes

- The reference map is anonymized and does not expose source project details.
- This plugin is intended to be adapted for different design domains.
