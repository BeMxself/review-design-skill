# review-design-skill

[English](README.md)

一个可复用的设计文档审查 Skill，支持人类与 AI 的多轮协作审查。

## 插件内容

- 可复用 Skill：`design-review-workflow`
- 动态审查计划（不是固定轮次顺序）
- 反过拟合防护（避免被单一领域经验绑死）
- 人类主导收口机制

## 目录结构

```text
skills/
  design-review-workflow/
    SKILL.md
    agents/openai.yaml
    references/
      external-auth-review-dimension-map.md
```

## 安装

### Claude Code（使用 `/plugin`）

使用 Claude Code 的 `/plugin` 命令安装本仓库，然后启用 `design-review-workflow`。

### Codex

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
ln -s /绝对路径/review-design-skill/skills/design-review-workflow "${CODEX_HOME:-$HOME/.codex}/skills/design-review-workflow"
```

### kiro-cli

```bash
mkdir -p ~/.kiro/skills
ln -s /绝对路径/review-design-skill/skills/design-review-workflow ~/.kiro/skills/design-review-workflow
```

## 使用方式

在审查设计文档时使用 `design-review-workflow`。

不同平台调用语法不同，但 Skill 内容不应绑定平台：
- Claude Code：`/design-review-workflow`
- Codex：`$design-review-workflow`

典型流程：
1. Round 0：先做文档原型分类并产出初始审查计划
2. Round N：逐轮输出问题、记录人类决策、动态重排优先级
3. 结束：必须由人类明确确认收口

## 说明

- 参考映射文档已匿名化，不暴露来源项目细节。
- 该插件可迁移到不同类型的设计文档审查场景。
