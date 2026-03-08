# Contributing

Contributions should preserve the multi-skill repository layout.

## Skill Directory Contract

Add each skill under `skills/<skill-slug>/`.

```text
skills/<skill-slug>/
├── SKILL.md
├── agents/
│   └── openai.yaml        # Optional; include when the skill supports Codex
├── references/            # Optional
└── examples/              # Optional
```

Rules:

- Do not add `SKILL.md`, `agents/`, `references/`, or workflow sample files to the repository root.
- `SKILL.md` is the canonical skill body for all platforms.
- Claude Code support should come directly from `SKILL.md`; do not create a separate Claude-only config unless the platform requires it.
- If the skill supports Codex, add `agents/openai.yaml` in the same skill directory and keep it aligned with `SKILL.md`.
- Prefer lowercase kebab-case for `<skill-slug>`.

## Adding a New Skill

1. Create `skills/<skill-slug>/SKILL.md` with YAML frontmatter:

```markdown
---
name: your-skill-name
description: When and how this skill should be used.
version: "1.0"
---

# Skill Title

(Skill instructions here)
```

2. Add `references/` only for material that should be loaded on demand.
3. Add `examples/` only for checked-in samples that help explain the skill.
4. Add `agents/openai.yaml` only if the skill should surface cleanly in Codex/OpenAI interfaces.
5. Update [README.md](/Users/miau/Documents/llm-skills/README.md) to list the new skill.

## Modifying an Existing Skill

- Keep paths and relative links valid from the skill directory itself.
- Bump the version number in `SKILL.md` when the workflow meaningfully changes.
- If you change skill-facing naming or positioning, update `agents/openai.yaml` in the same commit.
- If examples are committed, keep them under that skill's `examples/` directory.

## Pull Requests

1. Create a feature branch.
2. Make the skill and documentation changes together.
3. Explain which skills changed, whether Codex metadata changed, and whether examples were added or updated.
