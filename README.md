# llm-skills ✦

[简体中文](README.zh-CN.md)

### ⚡ Curated AI coding productivity skills for daily coding work.

Think first. Refactor later. Keep the token budget alive.

Not a giant skill catalog. Not a random prompt dump. This repository is a curated set of AI coding productivity skills for engineers who ship with AI every day and want sharper defaults for high-value work: better first prompts, safer refactors, less token waste, and reusable workflows that actually pull their weight.

## 🧠 Start With The Pain

- `roundtable-design-review`: **Pain** the first prompt is weak, the design is still fuzzy, and the refactor is already moving. **Intervention** force a cross-model review loop before execution. **Outcome** stronger plans, sharper prompts, and fewer expensive wrong turns.
- `token-guard`: **Pain** long sessions, oversized tool output, and Claude token ceilings can quietly kill execution halfway through. **Intervention** run a lightweight precheck, escalate only the risky work, and surface drift when actual token cost grows far beyond the original estimate. **Outcome** less token waste, fewer context blowups, and better odds the job actually finishes.

## 🚀 Why This Repo

- Curated AI coding productivity skills, not a kitchen-sink skill collection.
- One repository, multiple skills, one predictable layout.
- Shared `SKILL.md` instructions across platforms.
- Optional Codex metadata via `agents/openai.yaml`.
- Lightweight global guidance where it helps, detailed workflows only when needed.
- Checked-in examples and references instead of hidden tribal knowledge.

## 🌟 Flagship Skills

### 👥 `roundtable-design-review`

Structured multi-model design workflow for proposal, critique, revision, and final review. Use it when one model should author, peers should review, and the human should arbitrate without mixing design artifacts into the main codebase.

- Skill definition: `skills/roundtable-design-review/SKILL.md`
- Codex metadata: `skills/roundtable-design-review/agents/openai.yaml`
- Templates: `skills/roundtable-design-review/references/templates.md`
- Sample sessions: `skills/roundtable-design-review/examples/sessions/`

Workflow:

```text
User idea -> Author drafts V1 -> Reviewers critique -> Human arbitrates
  -> Author revises V2 -> Reviewers give final verdict
  -> All approved -> Done / Any blocker -> Author fixes V3 -> Done
```

### 🛡️ `token-guard`

Escalation-only token budget guardrail for expensive sessions. Use it to catch the patterns that quietly burn context and budget: long-session bloat, repo-wide scans, tool loops, oversized tool output, repeated background, heavy MCP exposure, or mid-session switching of model, thinking mode, or tool strategy. When a task is allowed, it still re-checks execution drift and can report when actual usage ends up far above the coarse forecast.

- Skill definition: `skills/token-guard/SKILL.md`
- Codex metadata: `skills/token-guard/agents/openai.yaml`
- Sample scenarios: `skills/token-guard/examples/`
- Companion global precheck: `CLAUDE.md`
- Setup guide: `docs/setup.md`

Layering model:

```text
CLAUDE.md lightweight precheck
  -> low/medium risk: proceed normally
  -> high/extreme risk or explicit TokenGuard request: invoke token-guard
  -> token-guard intercepts, narrows scope, or allows with a coarse estimate
  -> if actual execution drifts far above that estimate, token-guard re-intercepts or adds a drift warning
```

Example prompts:

```text
Use $token-guard to assess this request before doing it:
"Continue this long session, scan the whole repo, fix issues, run tests, and give me a full report."

Use $token-guard for an explicit check:
"Evaluate whether this task is too expensive before proceeding: review these 20 files and summarize everything."
```

## Recent Updates

- `2026-03-09`: `token-guard` now records an allow-time baseline, prefers host usage telemetry when available, reports meaningful end-of-turn drift when actual usage lands 2+ bands above the estimate, and reuses the existing interception flow when execution expands into `large` or `extreme`.
- `2026-03-09`: Added sample scenarios under `skills/token-guard/examples/` for allow-without-drift, allow-with-end-of-turn-drift, and execution-time re-interception.

## Quick Start

### Claude Code

1. Copy the desired `skills/<skill-slug>/SKILL.md` into your Claude Code skills directory.
2. If you install `token-guard`, also install the repository root `CLAUDE.md` as your lightweight global precheck.
3. Keep examples and references in the repo; load them only when needed.

### Codex

1. Use `skills/<skill-slug>/SKILL.md` as the skill body.
2. Use `skills/<skill-slug>/agents/openai.yaml` for Codex/OpenAI interface metadata.
3. For `token-guard`, also carry over the root `CLAUDE.md` if you want the always-on lightweight precheck layer.

### Manual Use

1. Load `skills/<skill-slug>/SKILL.md` into the target model.
2. Read any referenced files from that skill's `references/` directory on demand.
3. If the skill generates workflow artifacts, create a working directory exactly as described by the skill.
4. Treat `examples/` as checked-in samples, not as the live working directory unless the skill explicitly says otherwise.

## Repository Layout

Each skill lives under `skills/<skill-slug>/` and owns its own instructions, metadata, references, and examples.

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

## Platform Support

| Platform | Consumes | Notes |
| --- | --- | --- |
| Claude Code | `skills/<skill-slug>/SKILL.md` | Shared skill body; root `CLAUDE.md` is optional global guidance |
| Codex / OpenAI-compatible agents | `skills/<skill-slug>/SKILL.md` + `skills/<skill-slug>/agents/openai.yaml` | `openai.yaml` provides UI-facing metadata |

## Docs

- Setup and installation: `docs/setup.md`
- Chinese setup guide: `docs/setup.zh-CN.md`
- Contribution rules: `CONTRIBUTING.md`
- Chinese contribution guide: `CONTRIBUTING.zh-CN.md`

Note: the root `CLAUDE.md` remains concise and single-language on purpose, because it is loaded into model context and should stay as small as possible.

## Contributing

Contributions should preserve the shared multi-skill layout and keep skills usable across platforms. See `CONTRIBUTING.md` for the directory contract and contribution workflow.

## License

[MIT](LICENSE)
