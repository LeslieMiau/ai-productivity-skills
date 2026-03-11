# AI Coding Skills

[English](README.en.md)

> for Claude Code and Codex

给每天用 Claude Code 和 Codex 写代码的工程师准备的一组精选 AI coding skills。
- 痛点 1：first prompt 不够强，高风险设计和重构在没想清楚前就开始执行。
- 痛点 2：长会话、全仓扫描和大体量工具输出悄悄吞掉 token 与上下文。
- 旗舰 skills：[`roundtable-design-review`](skills/roundtable-design-review/SKILL.md) · [`token-guard`](skills/token-guard/SKILL.md)
- 平台支持：Claude Code · Codex / OpenAI-compatible agents
- 5 分钟开始：[安装指南](docs/setup.zh-CN.md) · [设计评审示例](skills/roundtable-design-review/examples/sessions/quant-backtest/) · [Token Guard 示例](skills/token-guard/examples/)

> Curated AI coding skills for Claude Code and Codex: stronger prompts, safer design reviews, and token-aware execution.

这不是一个大而全的 skill 收集器，也不是零散 prompt 的堆放区。它只收那些能在真实 AI coding 工作里提高命中率、降低返工和节省 token 的高价值 skills。

## 适合谁 / 不适合谁

- 适合：每天都在用 Claude Code、Codex 或兼容代理写代码的工程师。
- 适合：想把“先想清楚，再执行”变成稳定工作流的人。
- 适合：需要把 skill 以 `SKILL.md` + `agents/openai.yaml` 形式跨平台复用的人。
- 不适合：只想收集海量 prompt，而不关心可维护结构和复用方式的人。
- 不适合：期待一个覆盖所有模型、所有场景、所有偏好的大而全目录的人。

## 典型用法

- 高风险重构前：先用 `roundtable-design-review` 做多模型设计评审，再进入实现。
- 长会话继续做事前：先用 `token-guard` 判断是否该缩小范围、换新会话，还是可以直接放行。
- 需要跨平台共享 skill 时：保留统一目录结构，让 Claude Code 和 Codex 复用同一份 skill 正文。

## Start Here

1. 如果你现在最怕“设计没想透就开干”，从 [`skills/roundtable-design-review/SKILL.md`](skills/roundtable-design-review/SKILL.md) 开始。
2. 如果你现在最怕“token 和上下文半路炸掉”，从 [`skills/token-guard/SKILL.md`](skills/token-guard/SKILL.md) 开始。
3. 如果你要实际安装，先读 [`docs/setup.zh-CN.md`](docs/setup.zh-CN.md)；如果你要发仓库，继续读 [`docs/github-metadata.md`](docs/github-metadata.md) 和 [`docs/launch.md`](docs/launch.md)。

## 旗舰 Skills

### `roundtable-design-review`

- 问题：first prompt 还弱，设计仍然模糊，但高风险改动已经准备开始。
- 干预：强制走 Author -> Reviewers -> Human arbitration -> Revision 的结构化设计评审。
- 结果：在写业务代码前得到更强的方案、更清晰的取舍和更少的昂贵返工。
- 入口：[`SKILL.md`](skills/roundtable-design-review/SKILL.md) · [`openai.yaml`](skills/roundtable-design-review/agents/openai.yaml) · [`templates.md`](skills/roundtable-design-review/references/templates.md) · [`sample session`](skills/roundtable-design-review/examples/sessions/quant-backtest/)

```text
用户提出想法 -> Author 起草 V1 -> Reviewers 评审 -> 人工裁决
  -> Author 修订 V2 -> Reviewers 给最终结论
  -> 全部通过 -> 完成 / 有阻塞 -> Author 修复 V3 -> 完成
```

### `token-guard`

- 问题：长会话、全仓扫描、工具循环和大体量输出会悄悄烧掉 token，最后在半路失速。
- 干预：先做轻量预检，只把真正高风险的任务升级到 Token Guard，并在执行漂移时追加反馈。
- 结果：更少上下文爆炸、更少无效扫描，以及更高的任务完工率。
- 入口：[`SKILL.md`](skills/token-guard/SKILL.md) · [`openai.yaml`](skills/token-guard/agents/openai.yaml) · [`examples`](skills/token-guard/examples/) · [`setup`](docs/setup.zh-CN.md)

```text
CLAUDE.md 轻量预检
  -> 低/中风险：正常执行
  -> 高/极高风险或显式请求 TokenGuard：调用 token-guard
  -> token-guard 拦截、缩小范围，或给出粗粒度放行估算
  -> 若执行中实际消耗显著超估，则重新拦截或追加偏差提醒
```

## 为什么做这个仓库

- 它是 curated AI coding skills，不是 kitchen-sink skill collection。
- 同一份 `SKILL.md` 可以跨平台复用，必要时再配 `agents/openai.yaml`。
- 该常驻的保持轻量，该详细的只在需要时加载。
- 示例和参考文档都放在仓库里，便于复查、转发和二次打包。

## 平台支持

| 平台 | 读取内容 | 说明 |
| --- | --- | --- |
| Claude Code | `skills/<skill-slug>/SKILL.md` | 共享 skill 正文；根目录 `CLAUDE.md` 可作为轻量全局指令 |
| Codex / OpenAI-compatible agents | `skills/<skill-slug>/SKILL.md` + `skills/<skill-slug>/agents/openai.yaml` | `openai.yaml` 提供 UI 元数据 |

## 仓库结构

```text
.
├── skills/
│   └── <skill-slug>/
│       ├── SKILL.md
│       ├── agents/
│       │   └── openai.yaml
│       ├── references/
│       └── examples/
├── docs/
│   ├── assets/
│   ├── releases/
│   ├── github-metadata.md
│   ├── launch.md
│   ├── setup.md
│   └── setup.zh-CN.md
├── CLAUDE.md
├── CONTRIBUTING.md
├── CONTRIBUTING.zh-CN.md
├── LICENSE
├── README.en.md
├── README.md
└── README.zh-CN.md
```

`README.zh-CN.md` 仅作为旧链接兼容入口保留，中文默认首页现在是 `README.md`。

## 文档与发布

- 安装说明（中文）：[`docs/setup.zh-CN.md`](docs/setup.zh-CN.md)
- Setup guide (English): [`docs/setup.md`](docs/setup.md)
- GitHub 元数据与发布清单：[`docs/github-metadata.md`](docs/github-metadata.md)
- Launch 文案素材：[`docs/launch.md`](docs/launch.md)
- `v0.1.0` release notes：[`docs/releases/v0.1.0.md`](docs/releases/v0.1.0.md)
- 贡献规范：[`CONTRIBUTING.zh-CN.md`](CONTRIBUTING.zh-CN.md) · [`CONTRIBUTING.md`](CONTRIBUTING.md)

说明：根目录 `CLAUDE.md` 保持精简且单语，因为它会直接进入模型上下文，应该尽量短。

## 许可证

[MIT](LICENSE)
