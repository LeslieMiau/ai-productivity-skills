# 贡献指南

[English](CONTRIBUTING.md)

贡献应当让仓库更可复用，而不是更杂乱。

这个仓库围绕“可移植 skill”组织：每个 skill 独立维护自己的指令、可选平台元数据、可选参考文档和可选示例。请保持这套结构不被破坏。

## 仓库约定

每个 skill 都应放在 `skills/<skill-slug>/` 下。

```text
skills/<skill-slug>/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
└── examples/
```

规则：

- `SKILL.md` 是唯一权威的 skill 正文。
- 不要把 skill 正文、参考文档或示例流程文件放到仓库根目录。
- 如果一个 skill 支持 Codex 或 OpenAI 兼容接口，请把 `agents/openai.yaml` 放在同一个 skill 目录，并保持与 `SKILL.md` 一致。
- skill slug 使用小写 kebab-case。
- `references/` 只放那些需要按需加载的材料。
- `examples/` 只放那些真正有助于说明或验证 skill 的仓库内示例。

## 新建 Skill

1. 创建带 YAML frontmatter 的 `skills/<skill-slug>/SKILL.md`。
2. 如果这个 skill 需要在 Codex/OpenAI 界面中良好展示，就添加 `agents/openai.yaml`。
3. 只有在 `references/` 和 `examples/` 确实具有复用价值时才添加。
4. 更新 `README.md` 和 `README.en.md`，让新 skill 在中文默认首页和英文 README 中都可被发现。
5. 如果 skill 依赖根目录的配套文件，例如 `CLAUDE.md`，请在 skill 和仓库文档中都写清楚这种关系。

最小 frontmatter 示例：

```markdown
---
name: your-skill-name
description: What the skill does and when it should be used.
---
```

## 更新已有 Skill

- 保证相对路径和链接从 skill 目录出发仍然有效。
- 只要 skill 的定位、命名或调用方式变了，就同步更新 `agents/openai.yaml`。
- 安装流程或使用预期发生变化时，同步更新文档。
- 优先收紧说明，不要堆砌冗长解释。

## 提交 PR

1. 在同一个 PR 中同时提交 skill 变更和配套文档更新。
2. 说明改了哪些 skill，以及元数据、示例或安装文档是否也发生了变化。
3. 保持提交聚焦、易于审查。
