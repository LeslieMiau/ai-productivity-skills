# llm-skills

Reusable skills for LLM coding assistants. This repository now supports multiple skills in one repo, with a shared layout that works for both Claude Code and Codex.

## Repository Layout

Each skill lives under `skills/<skill-slug>/` and owns its own instructions, platform metadata, references, and examples.

```text
.
├── skills/
│   └── <skill-slug>/
│       ├── SKILL.md              # Shared skill instructions; Claude Code consumes this directly
│       ├── agents/
│       │   └── openai.yaml       # Optional Codex/OpenAI UI metadata
│       ├── references/           # Optional reference docs loaded on demand
│       └── examples/             # Optional checked-in examples for the skill
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Platform Support

The repository uses one canonical `SKILL.md` per skill.

| Platform | Consumes | Notes |
|---|---|---|
| Claude Code | `skills/<skill-slug>/SKILL.md` | No separate Claude-specific config file by default |
| Codex / OpenAI-compatible agents | `skills/<skill-slug>/SKILL.md` + `skills/<skill-slug>/agents/openai.yaml` | `openai.yaml` provides UI-facing metadata |

## Skills

### `roundtable-design-review`

Structured multi-model design workflow. One model authors a proposal, peer models review it, and the process converges in at most three proposal versions. Live artifacts should be stored in isolated session directories such as `AI_SESSIONS_HOME/roundtable-design-review/<workspace-slug>/<session-slug>/`, not mixed into business docs or source folders.

- Skill definition: `skills/roundtable-design-review/SKILL.md`
- Codex metadata: `skills/roundtable-design-review/agents/openai.yaml`
- Templates: `skills/roundtable-design-review/references/templates.md`
- Checked-in sample sessions: `skills/roundtable-design-review/examples/sessions/`

Workflow:

```text
User idea → Author drafts V1 → Reviewers critique → Human arbitrates
  → Author revises V2 → Reviewers give final verdict
  → All approved → Done / Any blocker → Author fixes V3 → Done
```

### `token-guard`

Escalation-only token budget guardrail. Use it to block or narrow only genuinely expensive task patterns such as long-session bloat, repo-wide scans, multi-step tool loops, oversized tool output, repeated long prompt background, excessive MCP exposure, or mid-session switching of model, thinking mode, or tool strategy.

- Skill definition: `skills/token-guard/SKILL.md`
- Codex metadata: `skills/token-guard/agents/openai.yaml`
- Global lightweight precheck: `CLAUDE.md`

Layering model:

```text
CLAUDE.md lightweight precheck
  → low/medium risk: proceed normally
  → high/extreme risk or explicit TokenGuard request: invoke token-guard
  → token-guard either intercepts, suggests cheaper alternatives, or allows with a coarse estimate
```

Example prompts:

```text
Use $token-guard to assess this request before doing it:
"Continue this long session, scan the whole repo, fix issues, run tests, and give me a full report."

Use $token-guard for an explicit check:
"Evaluate whether this task is too expensive before proceeding: review these 20 files and summarize everything."
```

## Usage

### Claude Code

Install or copy the desired `skills/<skill-slug>/SKILL.md` into your Claude Code skills directory.

If you want the lightweight always-on precheck for `token-guard`, also copy the repository root `CLAUDE.md` into your Claude Code global instructions.

### Codex

Use the same `SKILL.md` as the skill body and `agents/openai.yaml` for the Codex/OpenAI interface metadata.

### Manual Use

1. Load `skills/<skill-slug>/SKILL.md` into the target model.
2. Read any referenced files from that skill's `references/` directory as needed.
3. If the skill generates workflow artifacts, create a working directory in your project as described by the skill.
4. Treat `examples/` as repository samples, not as the live working directory unless the skill explicitly says otherwise.

## License

[MIT](LICENSE)
