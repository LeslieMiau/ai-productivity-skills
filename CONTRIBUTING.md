# Contributing

Contributions should make the repository more reusable, not more cluttered.

This repo is organized around portable skills: each skill owns its instructions, optional platform metadata, optional references, and optional examples. Keep that contract intact.

## Repository Contract

Add each skill under `skills/<skill-slug>/`.

```text
skills/<skill-slug>/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
└── examples/
```

Rules:

- `SKILL.md` is the canonical skill body.
- Do not add skill bodies, references, or sample workflow files to the repository root.
- If a skill supports Codex or OpenAI-compatible interfaces, keep `agents/openai.yaml` in the same skill directory and keep it aligned with `SKILL.md`.
- Use lowercase kebab-case for the skill slug.
- Add `references/` only for material that should be loaded on demand.
- Add `examples/` only for checked-in examples that help explain or validate the skill.

## Creating a New Skill

1. Create `skills/<skill-slug>/SKILL.md` with YAML frontmatter.
2. Add `agents/openai.yaml` if the skill should surface cleanly in Codex/OpenAI interfaces.
3. Add `references/` and `examples/` only when they carry real reusable value.
4. Update `README.md` so the new skill is discoverable.
5. If the skill relies on a root-level companion file such as `CLAUDE.md`, document that relationship in both the skill and the repo docs.

Minimal frontmatter example:

```markdown
---
name: your-skill-name
description: What the skill does and when it should be used.
---
```

## Updating an Existing Skill

- Keep links and relative paths valid from the skill directory.
- Update `agents/openai.yaml` whenever the skill's positioning, name, or invocation guidance changes.
- Keep docs in sync when installation flow or usage expectations change.
- Prefer tightening instructions over adding verbose explanation.

## Pull Requests

1. Make the skill change and the supporting documentation change in the same PR.
2. Explain which skills changed and whether metadata, examples, or setup docs changed.
3. Keep commits focused and reviewable.
