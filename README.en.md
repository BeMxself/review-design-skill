# review-design-skill

[English](README.en.md) | [中文](README.md)

A reusable design-document review skill for iterative human+AI collaboration.

## What this plugin provides

- A reusable skill: `review-design`
- Dynamic review planning (not rigid round order)
- Anti-overfitting guardrails (avoid domain lock-in)
- Human-gated closure protocol

## Repository layout

```text
skills/
  review-design/
    SKILL.md
    agents/openai.yaml
    references/
      external-auth-review-dimension-map.md
```

## Install

### Claude Code (Plugin)

Install in Claude Code by adding this GitHub repo as a plugin marketplace:

```text
/plugin marketplace add BeMxself/review-design-skill
```

Then install the plugin:

```text
/plugin install review-design@BeMxself-review-design-skill
```

After installation, `/review-design` is available.

### Codex CLI (Skills)

Codex scans skill folders from:
- repository path chain: `./.agents/skills/` up to repo root
- user path: `~/.agents/skills/`

For this project, use user-level install only (no need to create `.agents/skills` in this repo):

```bash
mkdir -p ~/.agents/skills
rsync -a skills/ ~/.agents/skills/
```

To update later, re-run the same `rsync` command.

### Kiro CLI

1) Install skills into the current workspace:
```bash
mkdir -p .kiro/skills
rsync -a skills/ .kiro/skills/
```

2) Create an agent (this opens an editor):
```bash
mkdir -p .kiro/agents
kiro-cli agent create --name "Review Design Agent" --directory .kiro/agents
```

3) In `.kiro/agents/review_design_agent.json`, add:
```json
{ "resources": ["skill://.kiro/skills/**/SKILL.md"] }
```

4) Start chat with that agent:
```bash
kiro-cli chat --agent "Review Design Agent"
```

## Use

Use `review-design` when reviewing design documents.

Platform invocation syntax is different, but the skill content should stay platform-agnostic:
- Claude Code: `/review-design`
- Codex: run `/skills`, then type `$review-design`

Typical flow:
1. Round 0: classify document archetype + build initial review plan
2. Round N: iterative findings + decision log + plan rebalance
3. Close only after explicit human decision

## Notes

- The reference map is anonymized and does not expose source project details.
- This plugin is intended to be adapted for different design domains.
