# llm-skills ✦

[English](README.md)

### ⚡ 给日常 AI coding 准备的精选生产力 skills。

先想清楚，再动手；先控住 token，再跑长链路。

这不是一个大而全的 skill 收集器，也不是零散 prompt 的堆放区。它面向那些每天都在用 AI 写代码的工程师，提供一组真正有用的 AI coding 生产力 skills：让 first prompt 更强、让高风险改动先被想透、让 token 别在半路悄悄烧光。

## 🧠 先看最痛的问题

- `roundtable-design-review`：**痛点** first prompt 还没想透、设计方案还没压实、高风险重构却已经开始推进。**干预** 先走一轮跨模型 review，再允许执行。**结果** 更强的 prompt、更稳的方案，以及更少的昂贵返工。
- `token-guard`：**痛点** 长会话、超大工具输出和 Claude token 上限，最容易把一条好链路直接做死在半路。**干预** 先做轻量预检，只把真正高风险的任务升级到 TokenGuard。**结果** 更少 token 浪费、更少上下文爆炸，以及更高的任务完工率。

## 🚀 为什么做这个仓库

- 这是精选的 AI coding 生产力 skills，不是“什么都收”的 skill 仓库。
- 一个仓库，多个 skill，一套稳定目录结构。
- 用同一份 `SKILL.md` 适配多个平台。
- 通过 `agents/openai.yaml` 提供可选的 Codex 元数据。
- 该常驻的保持轻量，该详细的只在需要时加载。
- 用仓库内示例和参考文档代替口口相传的隐性知识。

## 🌟 旗舰 Skills

### 👥 `roundtable-design-review`

面向多模型协作设计评审的结构化工作流。适合让一个模型产出方案、多个模型审查、由人来裁决。

- Skill 定义：`skills/roundtable-design-review/SKILL.md`
- Codex 元数据：`skills/roundtable-design-review/agents/openai.yaml`
- 模板：`skills/roundtable-design-review/references/templates.md`
- 示例会话：`skills/roundtable-design-review/examples/sessions/`

工作流：

```text
用户提出想法 -> Author 起草 V1 -> Reviewers 评审 -> 人工裁决
  -> Author 修订 V2 -> Reviewers 给最终结论
  -> 全部通过 -> 完成 / 有阻塞 -> Author 修复 V3 -> 完成
```

### 🛡️ `token-guard`

别让 token 黑洞拖垮执行。`token-guard` 是只在高风险场景启用的 Token 守卫 skill，专门拦截那些悄悄吞噬上下文和预算的模式，例如长会话膨胀、全仓扫描、工具循环、超大工具输出、重复背景、过重的 MCP 暴露，以及中途切模型、切 thinking、切工具策略。

- Skill 定义：`skills/token-guard/SKILL.md`
- Codex 元数据：`skills/token-guard/agents/openai.yaml`
- 配套全局预检：`CLAUDE.md`
- 安装指南：`docs/setup.zh-CN.md`

分层模型：

```text
CLAUDE.md 轻量预检
  -> 低/中风险：正常执行
  -> 高/极高风险或显式请求 TokenGuard：调用 token-guard
  -> token-guard 拦截、缩小范围，或给出粗粒度放行估算
```

示例提示：

```text
Use $token-guard to assess this request before doing it:
"Continue this long session, scan the whole repo, fix issues, run tests, and give me a full report."

Use $token-guard for an explicit check:
"Evaluate whether this task is too expensive before proceeding: review these 20 files and summarize everything."
```

## 快速开始

### Claude Code

1. 将目标 `skills/<skill-slug>/SKILL.md` 复制到 Claude Code 的 skills 目录。
2. 如果你安装 `token-guard`，也请把仓库根目录的 `CLAUDE.md` 作为轻量全局预检一起安装。
3. 示例和参考文档可以保留在仓库里，需要时再按需读取。

### Codex

1. 用 `skills/<skill-slug>/SKILL.md` 作为 skill 正文。
2. 用 `skills/<skill-slug>/agents/openai.yaml` 提供 Codex/OpenAI 的界面元数据。
3. 对于 `token-guard`，如果你也想要常驻的轻量预检层，请同时带上根目录 `CLAUDE.md`。

### 手动使用

1. 将 `skills/<skill-slug>/SKILL.md` 加载到目标模型。
2. 按需读取该 skill 下 `references/` 目录中的参考文件。
3. 如果该 skill 会生成流程文件，请严格按 skill 的说明创建工作目录。
4. `examples/` 是仓库内示例，不是实际工作目录，除非 skill 明确说明可以直接使用。

## 仓库结构

每个 skill 都放在 `skills/<skill-slug>/` 下，独立维护自己的指令、元数据、参考文档和示例。

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
├── CLAUDE.md
├── CONTRIBUTING.md
├── CONTRIBUTING.zh-CN.md
├── LICENSE
├── README.md
└── README.zh-CN.md
```

## 平台支持

| 平台 | 读取内容 | 说明 |
| --- | --- | --- |
| Claude Code | `skills/<skill-slug>/SKILL.md` | 共享 skill 正文；根目录 `CLAUDE.md` 是可选的全局指令 |
| Codex / OpenAI 兼容代理 | `skills/<skill-slug>/SKILL.md` + `skills/<skill-slug>/agents/openai.yaml` | `openai.yaml` 提供 UI 元数据 |

## 文档

- 安装说明：`docs/setup.zh-CN.md`
- English setup guide: `docs/setup.md`
- 贡献规范：`CONTRIBUTING.zh-CN.md`
- English contribution guide: `CONTRIBUTING.md`

说明：根目录 `CLAUDE.md` 有意保持精简且单语，因为它会直接进入模型上下文，应该尽量短。

## 贡献

欢迎贡献，但请保持多 skill 共用的仓库结构，并确保 skill 在多个平台上都可用。具体规则见 `CONTRIBUTING.zh-CN.md`。

## 许可证

[MIT](LICENSE)
