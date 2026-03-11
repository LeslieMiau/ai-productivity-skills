# 安装指南

[English](setup.md)

这份文档负责说明 AI Coding Skills 的安装方式。这是一个面向 Claude Code 和 Codex 的可复用 AI coding skills 仓库。

这个仓库支持两种安装方式：

- 只安装单个 skill。
- 当工作流确实需要时，同时安装 skill 和轻量全局指令。

## 安装单个 Skill

对于任意 skill：

1. 将 `skills/<skill-slug>/SKILL.md` 复制到目标助手的 skills 目录或技能注册位置。
2. 如果目标平台支持 UI 元数据，也一并复制 `skills/<skill-slug>/agents/openai.yaml`。
3. 如果你想保留完整体验，可以同时带上 `references/` 和 `examples/`。

## 安装 `token-guard`

`token-guard` 设计为双层结构，也可以把它理解成一套 Token 守卫机制：

- `CLAUDE.md` 中的全局轻量预检
- `skills/token-guard/SKILL.md` 中的高风险升级工作流

### Claude Code

推荐安装方式：

1. 把 `skills/token-guard/SKILL.md` 安装到 Claude Code 的 skills 目录。
2. 把仓库根目录的 `CLAUDE.md` 复制到 Claude Code 的全局指令中。

这样拆分的原因：

- `CLAUDE.md` 保持短小并常驻生效。
- `token-guard` 只在任务真的高风险或被显式调用时加载。
- 普通任务依旧保持快速、低干扰；即使任务被放行，若实际消耗后续明显失控，也还能补充偏差反馈。

### Codex

推荐安装方式：

1. 用 `skills/token-guard/SKILL.md` 作为 skill 正文。
2. 用 `skills/token-guard/agents/openai.yaml` 提供界面元数据。
3. 如果你希望高风险任务前也具备同样的轻量预检行为，请同时带上仓库根目录的 `CLAUDE.md`。

## TokenGuard 使用方式

推荐使用流程：

```text
new task
  -> lightweight precheck
  -> low/medium risk: do the task
  -> high/extreme risk: invoke token-guard
  -> token-guard intercepts or allows with a coarse estimate
  -> if actual execution grows far beyond the estimate, token-guard re-intercepts or reports drift at the end of the turn
```

需要时可以显式调用：

```text
Use $token-guard to assess this request before doing it:
"Continue this long session, scan the repo, run tools, and give me a complete report."
```

## 更新 Skill

当你更新一个 skill：

1. 保持 `SKILL.md` 与 `agents/openai.yaml` 一致。
2. 如果定位或安装流程变了，也同步更新仓库文档。
3. 如果 skill 依赖类似 `CLAUDE.md` 这样的根目录配套文件，就在两个位置都把关系说明清楚。
