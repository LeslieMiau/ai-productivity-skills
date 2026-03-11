# Launch Kit

Reusable launch copy for the `AI Coding Skills` repository.

## Rollout Order

1. Finalize `README.md`, `README.en.md`, and the social preview asset.
2. Apply the GitHub metadata from [`docs/github-metadata.md`](github-metadata.md).
3. Create tag `v0.1.0` and use [`docs/releases/v0.1.0.md`](releases/v0.1.0.md) for the release body.
4. Publish one English post and one Chinese post on the same day.
5. For the next two weeks, pair each meaningful update with a short release note and one short post.

## GitHub Release Summary

### English

`AI Coding Skills v0.1.0` is the first public release of a curated skills repo for Claude Code and Codex.

Included now:
- `roundtable-design-review` for structured multi-model design review before risky implementation.
- `token-guard` for catching long-session bloat, repo-wide scans, and token-heavy execution before they derail the task.

What makes this repo useful:
- Stronger first prompts and sharper design review loops.
- Token-aware execution instead of context blowups halfway through.
- Portable packaging with `SKILL.md`, optional `agents/openai.yaml`, checked-in examples, and setup docs.

Start here:
- Setup: [`docs/setup.md`](setup.md)
- Chinese default README: [`README.md`](../README.md)
- English README: [`README.en.md`](../README.en.md)

### 中文

`AI Coding Skills v0.1.0` 是这个仓库的首个公开版本，目标是给 Claude Code 和 Codex 提供一组真正能复用的 AI coding skills。

当前包含：
- `roundtable-design-review`：在高风险实现前先走多模型设计评审。
- `token-guard`：在长会话、全仓扫描和大体量输出失控前先做 Token 风险拦截。

这个仓库的价值：
- 让 first prompt 更强，让设计评审更稳。
- 让执行更有 token 意识，而不是在中途上下文爆炸。
- 用 `SKILL.md`、可选 `agents/openai.yaml`、示例和安装文档形成可移植封装。

入口：
- 安装说明：[`docs/setup.zh-CN.md`](setup.zh-CN.md)
- 中文默认 README：[`README.md`](../README.md)
- English README: [`README.en.md`](../README.en.md)

## X / Social Short Post

### English

Launched `AI Coding Skills`: a curated repo of reusable skills for Claude Code and Codex.

- `roundtable-design-review`: weak first prompt -> structured multi-model review -> stronger proposal before code
- `token-guard`: long session + repo scan -> precheck + escalation -> fewer token/context blowups

Repo: [README.en.md](../README.en.md)

### 中文

我把 `AI Coding Skills` 开源出来了：一组给 Claude Code 和 Codex 用的可复用 AI coding skills。

- `roundtable-design-review`：方案没想透 -> 先走多模型评审 -> 再进实现
- `token-guard`：长会话 + 全仓扫描 -> 先做预检和拦截 -> 更少 token / 上下文爆炸

仓库入口：[README.md](../README.md)

## 中文社区发帖模板

### 标题

开源一个给 Claude Code / Codex 用的 AI coding skills 仓库：AI Coding Skills

### 正文

最近把自己日常 AI coding 里真正反复会用到的两类能力整理成了一个开源仓库：`AI Coding Skills`。

它不是 prompt 大杂烩，而是可复用、可迁移的 skill 封装，先解决两个最常见的问题：

1. 设计没想透就开干，最后返工很重。
2. 长会话、全仓扫描和工具输出把 token / 上下文烧穿。

现在仓库里先放了两个 flagship skills：

- `roundtable-design-review`
  弱 first prompt -> Author / Reviewers / Human 仲裁 -> 更强方案再落地
- `token-guard`
  长会话 + 大范围扫描 -> 轻量预检 + 高风险升级拦截 -> 更少 context blowup

支持 Claude Code 和 Codex，结构上是统一的 `SKILL.md`，并可选补 `agents/openai.yaml`。

如果你也在用 AI 写代码，欢迎直接看默认中文 README：
[README.md](../README.md)

安装说明：
[docs/setup.zh-CN.md](setup.zh-CN.md)
