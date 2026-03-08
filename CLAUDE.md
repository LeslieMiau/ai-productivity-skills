# Global Claude Instructions

## Token Cost Guardrail

Before starting a new task, do a lightweight token-risk precheck.

Proceed normally for low or medium risk tasks.

Only escalate to the `token-guard` skill when the task shows clear high-risk token patterns, especially:
- long session plus a new complex task
- repo-wide or many-file scan
- multi-step tool loop
- large Bash, Web, or tool results likely to flow back into context
- repeated long rules or background in the prompt instead of keeping them in `CLAUDE.md`
- too many MCP tools exposed
- switching model, thinking mode, or tool strategy mid-session
- exhaustive or very long output requests

If the user explicitly asks to use TokenGuard, or sends a TokenGuard control command, invoke `token-guard`.

If the task is high or extreme risk, invoke `token-guard` before doing the task.
