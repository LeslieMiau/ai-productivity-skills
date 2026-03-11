# Setup Guide

[简体中文](setup.zh-CN.md)

Portable installation notes for AI Coding Skills, a curated repository of reusable AI coding skills for Claude Code and Codex.

This repository supports two installation styles:

- Install an individual skill only.
- Install a skill plus lightweight global guidance when the workflow benefits from it.

## Install a Skill

For any skill:

1. Copy `skills/<skill-slug>/SKILL.md` into the target assistant's skills directory or skill registry.
2. If the platform supports UI metadata, also copy `skills/<skill-slug>/agents/openai.yaml`.
3. Keep `references/` and `examples/` alongside the skill if you want the full packaged experience.

## Install `token-guard`

`token-guard` is designed as a two-layer setup:

- Global lightweight precheck in `CLAUDE.md`
- Detailed high-risk escalation workflow in `skills/token-guard/SKILL.md`

### Claude Code

Recommended setup:

1. Install `skills/token-guard/SKILL.md` into your Claude Code skills directory.
2. Copy the repository root `CLAUDE.md` into your Claude Code global instructions.

Why this split:

- `CLAUDE.md` stays short and always-on.
- `token-guard` only loads when the task is actually risky or explicitly requested.
- Normal tasks stay fast and low-friction, while allowed tasks can still report major estimate drift if real usage balloons.

### Codex

Recommended setup:

1. Use `skills/token-guard/SKILL.md` as the skill body.
2. Use `skills/token-guard/agents/openai.yaml` for the interface metadata.
3. Also carry over the repository root `CLAUDE.md` if you want the same lightweight precheck behavior before high-risk tasks.

## TokenGuard Usage Pattern

The intended flow is:

```text
new task
  -> lightweight precheck
  -> low/medium risk: do the task
  -> high/extreme risk: invoke token-guard
  -> token-guard intercepts or allows with a coarse estimate
  -> if actual execution grows far beyond the estimate, token-guard re-intercepts or reports drift at the end of the turn
```

Use explicit invocation when needed:

```text
Use $token-guard to assess this request before doing it:
"Continue this long session, scan the repo, run tools, and give me a complete report."
```

## Updating a Skill

When you update a skill:

1. Keep `SKILL.md` and `agents/openai.yaml` aligned.
2. Update repository docs if the positioning or installation flow changes.
3. If the skill depends on a root-level companion file like `CLAUDE.md`, document that relationship clearly in both places.
