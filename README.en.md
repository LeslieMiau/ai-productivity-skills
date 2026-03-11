# AI Coding Skills

[Chinese Default README](README.md)

> for Claude Code and Codex

Curated AI coding skills for engineers who ship with Claude Code and Codex every day.
- Pain 1: weak first prompts and fuzzy design work slipping into execution too early.
- Pain 2: long sessions, repo-wide scans, and oversized tool output quietly burning token budget and context.
- Flagship skills: [`roundtable-design-review`](skills/roundtable-design-review/SKILL.md) · [`token-guard`](skills/token-guard/SKILL.md)
- Platform support: Claude Code · Codex / OpenAI-compatible agents
- Start in 5 minutes: [setup guide](docs/setup.md) · [design review sample](skills/roundtable-design-review/examples/sessions/quant-backtest/) · [Token Guard examples](skills/token-guard/examples/)

> Curated AI coding skills for Claude Code and Codex: stronger prompts, safer design reviews, and token-aware execution.

This is not a giant skill catalog and not a prompt dump. It is a tight repository of reusable skills that improve how AI coding work gets started, reviewed, and completed.

## Who It's For / Not For

- For engineers who use Claude Code, Codex, or compatible coding agents as part of daily delivery work.
- For teams who want sharper defaults before high-risk implementation starts.
- For people who want portable `SKILL.md` workflows with optional `agents/openai.yaml` metadata.
- Not for people looking for a kitchen-sink directory of every possible prompt.
- Not for workflows that value quantity of skills over reuse, clarity, and packaging discipline.

## Use Cases

- Before a risky refactor: run `roundtable-design-review` to get proposal critique and revision before code changes.
- Before continuing an expensive session: run `token-guard` to decide whether to narrow scope, restart cleanly, or proceed.
- When sharing workflows across tools: keep one skill layout that works for Claude Code and Codex.

## Start Here

1. If your biggest problem is design risk, start with [`skills/roundtable-design-review/SKILL.md`](skills/roundtable-design-review/SKILL.md).
2. If your biggest problem is token and context blowups, start with [`skills/token-guard/SKILL.md`](skills/token-guard/SKILL.md).
3. If you are installing the repo, read [`docs/setup.md`](docs/setup.md); if you are launching the repo publicly, continue with [`docs/github-metadata.md`](docs/github-metadata.md) and [`docs/launch.md`](docs/launch.md).

## Flagship Skills

### `roundtable-design-review`

- Problem: the first prompt is weak, the design is still fuzzy, and high-risk changes are about to start anyway.
- Intervention: force an Author -> Reviewer -> Human arbitration -> Revision workflow before implementation.
- Result: stronger proposals, clearer tradeoffs, and fewer expensive wrong turns before touching business code.
- Entry points: [`SKILL.md`](skills/roundtable-design-review/SKILL.md) · [`openai.yaml`](skills/roundtable-design-review/agents/openai.yaml) · [`templates.md`](skills/roundtable-design-review/references/templates.md) · [`sample session`](skills/roundtable-design-review/examples/sessions/quant-backtest/)

```text
User idea -> Author drafts V1 -> Reviewers critique -> Human arbitrates
  -> Author revises V2 -> Reviewers give final verdict
  -> All approved -> Done / Any blocker -> Author fixes V3 -> Done
```

### `token-guard`

- Problem: long sessions, repo-wide scans, tool loops, and large outputs quietly eat token budget and context until execution collapses.
- Intervention: run a lightweight precheck, escalate only the risky work, and report drift when real execution grows far past the estimate.
- Result: fewer context blowups, less wasteful scanning, and better odds the task actually finishes.
- Entry points: [`SKILL.md`](skills/token-guard/SKILL.md) · [`openai.yaml`](skills/token-guard/agents/openai.yaml) · [`examples`](skills/token-guard/examples/) · [`setup`](docs/setup.md)

```text
CLAUDE.md lightweight precheck
  -> low/medium risk: proceed normally
  -> high/extreme risk or explicit TokenGuard request: invoke token-guard
  -> token-guard intercepts, narrows scope, or allows with a coarse estimate
  -> if actual execution drifts far above that estimate, token-guard re-intercepts or adds a drift warning
```

## Why This Repo

- It is a curated AI coding skills repo, not a kitchen-sink collection.
- The same `SKILL.md` can travel across platforms, with `agents/openai.yaml` added only when useful.
- Lightweight global guidance stays small; detailed workflows load only when needed.
- Examples and references are checked in so they can be inspected, shared, and repackaged.

## Platform Support

| Platform | Consumes | Notes |
| --- | --- | --- |
| Claude Code | `skills/<skill-slug>/SKILL.md` | Shared skill body; root `CLAUDE.md` can serve as lightweight global guidance |
| Codex / OpenAI-compatible agents | `skills/<skill-slug>/SKILL.md` + `skills/<skill-slug>/agents/openai.yaml` | `openai.yaml` provides UI-facing metadata |

## Repository Layout

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

`README.zh-CN.md` is kept as a compatibility redirect for older links. The default Chinese landing page now lives in `README.md`.

## Docs and Launch Assets

- Chinese setup guide: [`docs/setup.zh-CN.md`](docs/setup.zh-CN.md)
- Setup guide (English): [`docs/setup.md`](docs/setup.md)
- GitHub metadata and rollout checklist: [`docs/github-metadata.md`](docs/github-metadata.md)
- Launch copy kit: [`docs/launch.md`](docs/launch.md)
- `v0.1.0` release notes: [`docs/releases/v0.1.0.md`](docs/releases/v0.1.0.md)
- Contributing: [`CONTRIBUTING.md`](CONTRIBUTING.md) · [`CONTRIBUTING.zh-CN.md`](CONTRIBUTING.zh-CN.md)

Note: the root `CLAUDE.md` intentionally stays concise and single-language because it is meant to live in model context.

## License

[MIT](LICENSE)
