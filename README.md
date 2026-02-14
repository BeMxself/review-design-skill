# review-design-skill

[English](README.en.md) | [中文](README.md)

一个可复用的设计文档审查 Skill，支持人类与 AI 的多轮协作审查。

## 插件内容

- 可复用 Skill：`review-design`
- 动态审查计划（不是固定轮次顺序）
- 反过拟合防护（避免被单一领域经验绑死）
- 人类主导收口机制

## 目录结构

```text
skills/
  review-design/
    SKILL.md
    agents/openai.yaml
    references/
      external-auth-review-dimension-map.md
```

## 安装

### Claude Code（插件）

在 Claude Code 中把本 GitHub 仓库添加为插件市场（Marketplace）：

```text
/plugin marketplace add BeMxself/review-design-skill
```

然后安装插件：

```text
/plugin install review-design@BeMxself-review-design-skill
```

安装后即可使用 `/review-design`。

### Codex CLI（Skills）

Codex 会从以下路径扫描 skills：
- 仓库路径链：`./.agents/skills/`（从当前目录向上到仓库根目录）
- 用户目录：`~/.agents/skills/`

本项目建议只用用户目录安装（不需要在本仓库创建 `.agents/skills`）：

```bash
mkdir -p ~/.agents/skills
rsync -a skills/ ~/.agents/skills/
```

后续更新时，重复执行同样的 `rsync` 命令即可。

### Kiro CLI

1）将 skills 安装到当前 workspace：
```bash
mkdir -p .kiro/skills
rsync -a skills/ .kiro/skills/
```

2）创建一个 agent（该命令会打开编辑器）：
```bash
mkdir -p .kiro/agents
kiro-cli agent create --name "Review Design Agent" --directory .kiro/agents
```

3）在 `.kiro/agents/review_design_agent.json` 中加入：
```json
{ "resources": ["skill://.kiro/skills/**/SKILL.md"] }
```

4）使用该 agent 启动对话：
```bash
kiro-cli chat --agent "Review Design Agent"
```

## 使用方式

在审查设计文档时使用 `review-design`。

不同平台调用语法不同，但 Skill 内容不应绑定平台：
- Claude Code：`/review-design`
- Codex：先运行 `/skills`，再输入 `$review-design`

典型流程：
1. Round 0：先做文档原型分类并产出初始审查计划
2. Round N：逐轮输出问题、记录人类决策、动态重排优先级
3. 结束：必须由人类明确确认收口

## 说明

- 参考映射文档已匿名化，不暴露来源项目细节。
- 该插件可迁移到不同类型的设计文档审查场景。
