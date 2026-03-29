---
name: harness-engineering
description: "Set up and manage long-running autonomous agent harnesses for any software project. Use this skill whenever the user wants to: configure a project for autonomous coding sessions, set up anti-forgetting mechanisms for AI agents, create init.sh/PLAN.json/PROGRESS.md scaffolding, check harness progress, or clean up harness files after completion. Trigger on natural language too — phrases like 'harness 进度怎么样', '做到哪了', 'what's the status', 'harness 清理一下', 'done with the task', 'set up this project for codex', 'make claude code work autonomously here', or any mention of 'harness', 'long-running agent', 'autonomous coding', 'anti-forgetting'. If the user wants an agent to work on a multi-step task without losing context, this skill applies."
version: "2.1"
---

# Harness Engineering

This skill sets up projects for long-running autonomous agent sessions (Claude Code, Codex, or similar). It implements the patterns from Anthropic's ["Effective Harnesses for Long-Running Agents"](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) article.

## Why this matters

AI coding agents lose context when sessions are long — auto-compaction drops details, resumed sessions start blank, and the agent forgets what it already did. The harness pattern solves this by externalizing state to files that survive context resets:

```
┌──────────────────────────────────────┐
│  Agent Session                       │
│                                      │
│  PLAN.json    → what to do           │
│  PROGRESS.md  → what was done        │
│  git commits  → checkpoints          │
│  init.sh      → environment setup    │
│                                      │
│  Session start → read files → work   │
│  Session end   → commit + update     │
└──────────────────────────────────────┘
```

## Commands

This skill responds to three intents. Detect which one the user wants from natural language — no slash commands required:

| User might say | Action |
|----------------|--------|
| "帮我重构当前项目的架构" / "set up for codex" / "configure harness" / "帮我做一个多步任务" | **harness-init** |
| "harness 进度怎么样" / "做到哪了" / "what's the status" / "还剩多少" | **harness-status** |
| "harness 清理一下" / "任务完成了" / "done with the task" / "reset harness" | **harness-reset** |

If the user provides a task description (e.g. "帮我重构架构"), do harness-init AND decompose the task into PLAN.json. If they just want the scaffolding without a specific task, generate only init.sh + .gitignore.

---

## harness-init

### Step 0: Match the user's language

Detect the user's language from their message, existing docs (CLAUDE.md, README, comments), or project conventions. All generated files and responses should use the same language. If the user writes in Chinese, output in Chinese. If English, output in English.

### Step 1: Detect project type and current state

Read the project root to identify the stack:

| Marker file | Stack | Test runner | Dev server |
|-------------|-------|-------------|------------|
| `pyproject.toml` / `setup.py` / `requirements.txt` | Python | pytest | uvicorn / flask / django |
| `package.json` | Node.js | npm test / jest / vitest | npm start / next dev |
| `go.mod` | Go | go test | go run |
| `Cargo.toml` | Rust | cargo test | cargo run |

Also detect:
- Framework (FastAPI, Django, Next.js, etc.) by reading config files
- Existing test directories (`tests/`, `test/`, `__tests__/`, `spec/`)
- Existing dev server scripts (`scripts/run_local.sh`, `Makefile`, etc.)
- Health check endpoints (search for `/health`, `/ping`, `/preflight` in route files)
- Existing `init.sh` (skip generation if present)
- **The project's own test command** — read `package.json` scripts, `pyproject.toml [tool.pytest]`, `Makefile` test targets, etc. Always prefer the project's own test command over hardcoded runner names

**Critical: Assess existing work before planning.** Before generating PLAN.json, read `git log`, existing docs, code structure, and any prior plans/handoff docs. If the task is partially complete, only plan the remaining work — do not re-plan already-done work. This prevents agents from redoing completed features.

### Step 2: Generate init.sh

Create a project-specific `init.sh` in the project root. If `init.sh` already exists, skip this step entirely.

The script must:

1. **Never use `set -e`** — individual failures should be reported, not crash the whole script
2. **Always exit 0** — count errors and report them, but don't block the agent (this is intentional — the agent interprets errors itself)
3. **Be idempotent** — safe to run multiple times (skip if already running, etc.)
4. **Use the project's own commands** — if `package.json` has a `"test"` script, use `pnpm test` / `npm test` instead of `npx jest`. If a `Makefile` has a `test` target, use `make test`. Respect the project's tooling choices.

Template structure:

```bash
#!/usr/bin/env bash
# init.sh — Harness session startup script
# Auto-generated by harness-engineering skill
cd "$(dirname "$0")"
ERRORS=0

echo "=== 1. Environment ==="
# [stack-specific: create venv / install deps]

echo "=== 2. Tests ==="
# [stack-specific: run test suite using PROJECT'S OWN test command, non-fatal]

echo "=== 3. Dev Server ==="
# [stack-specific: start if not running, skip if already up]
# Skip this section entirely for libraries, CLI tools, or projects without a dev server

echo "=== 4. Health Check ==="
# [stack-specific: curl health endpoint if server was started]
# Skip this section if no dev server

echo "=== Init complete (errors: $ERRORS) ==="
exit 0
```

Read `references/init-templates.md` for stack-specific templates. Adapt them to the actual project — templates are starting points, not copy-paste targets.

Make the script executable: `chmod +x init.sh`

### Step 3: Update .gitignore

If `.gitignore` exists and doesn't already contain `PLAN.json`:

```
# Harness temporary files
PLAN.json
PROGRESS.md
```

If `.gitignore` doesn't exist, create it with these entries.

### Step 4: Decompose task into PLAN.json (if task provided)

If the user provided a task description, decompose it into a granular feature list.

**PLAN.json format:**

```json
[
  {
    "id": 1,
    "category": "setup",
    "description": "Short description of what this feature does",
    "steps": [
      "Concrete verification step 1",
      "Concrete verification step 2",
      "Concrete verification step 3"
    ],
    "passes": false
  }
]
```

**Decomposition guidelines:**

- Target **50+ features** for substantial tasks, 10-20 for smaller ones
- Each feature should be completable in a single session (30-60 min of agent work)
- Steps should be **E2E verification steps**, not implementation steps — describe how to confirm the feature works, not how to build it
- Categories: `setup`, `functional`, `api`, `ui`, `data`, `testing`, `integration`, `refactor`, `docs`
- Order features by dependency — earlier features should be prerequisites for later ones. Put testing features AFTER the code they test, not before.
- Use JSON, not Markdown — models are less likely to corrupt JSON
- **Only plan remaining work.** If the project already has completed features (check git log, existing code, handoff docs), do NOT re-plan them. Start the plan from where the project currently stands. Record what's already done in PROGRESS.md instead.

### Step 5: Create PROGRESS.md

```markdown
## Harness initialized — [DATE]
- Project type: [detected stack]
- Features planned: [count from PLAN.json, or "N/A" if no task]
- init.sh generated: yes / skipped (already exists)
- .gitignore updated: yes
- Existing work detected: [brief summary of what's already done, if any]
- Key decisions: [why this decomposition, what was excluded and why]
```

Include enough context that a future session can understand the initialization choices without re-reading the whole project.

### Step 6: Commit scaffolding

Stage and commit the generated files:

```bash
git add init.sh .gitignore PLAN.json PROGRESS.md  # only files that exist
git commit -m "harness: initialize project for autonomous agent sessions"
```

### Step 7: Report to user

Show a concise summary of what was created. List only the files that were actually generated (don't mention files that were skipped). Include a brief overview of the plan if PLAN.json was created.

Do NOT reference CLAUDE.md or AGENTS.md harness mode in the output — the user may not have those configured. Just tell them what files were created and that agents can use them to maintain context across sessions.

---

## harness-status

Read and report the current harness state:

1. **PLAN.json** — count total / completed / remaining features, list ALL remaining features (not just the next 3 — the user wants full visibility)
2. **PROGRESS.md** — show the last 2 session entries
3. **git log** — show last 10 commits with `--oneline`. Sanity-check that recent commits relate to the plan's features — if they don't match (e.g. different project context), note this discrepancy
4. **init.sh** �� confirm it exists and is executable. If missing, suggest running harness-init to regenerate

Format as a concise dashboard:

```
Harness Status: [project-name]

Progress: 12/45 features complete (27%)

Remaining:
  #13 — User can filter strategies by performance
  #14 — Strategy detail page shows historical returns
  #15 — Export strategy backtest results to CSV
  ... [list all remaining]

Last session (2026-03-28 14:30):
  - Completed #12: strategy list pagination
  - Decision: used cursor-based pagination for consistency with existing API

Recent commits:
  abc1234 feat: add cursor-based pagination to strategy list
  def5678 feat: implement strategy sorting by return rate
  ...
```

---

## harness-reset

Clean up harness files. This happens in two scenarios:

### Auto-cleanup (all features complete)
When all features in PLAN.json have `passes: true`, the agent should **automatically** clean up without asking:

1. Append completion summary to PROGRESS.md
2. `git rm PLAN.json PROGRESS.md`
3. `git commit -m "harness: all features complete, cleanup"`
4. Report completion to user

This ensures the workspace is clean for the next harness task.

### Manual cleanup (user triggers)
When the user says "harness 清理一下" / "reset harness" / "任务完成了":

1. Check PLAN.json — warn if there are incomplete features, ask to confirm
2. Remove `PLAN.json` and `PROGRESS.md`
3. Keep `init.sh` and `.gitignore` entries (they're useful for future sessions)
4. Commit the cleanup:

```bash
git rm PLAN.json PROGRESS.md
git commit -m "harness: clean up task files"
```

---

## Design Principles (for reference)

These are baked into AGENTS.md / CLAUDE.md globally, but understanding them helps when decomposing tasks:

1. **External state over memory** — PLAN.json + PROGRESS.md + git survive context resets
2. **JSON over Markdown for specs** — models resist corrupting structured JSON
3. **Immutable spec** — only `passes` field changes in PLAN.json; description/steps are read-only
4. **Continuous loop** — complete a feature → commit → pick next → repeat. Never stop between features unless blocked
5. **Commit as checkpoint** — every feature completion = git commit + PLAN.json update + PROGRESS.md append. This is the recovery point
6. **E2E verification** — features aren't done until verified through the full user flow
7. **Session-end commit** — always commit progress, even if incomplete
8. **Git recovery** — stash/revert when existing features break
9. **Non-fatal init** — init.sh reports errors but never blocks the agent
10. **Auto-cleanup** — when all features pass, delete PLAN.json + PROGRESS.md so the next task starts clean
