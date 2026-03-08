---
name: token-guard
description: "Escalation-only token budget guardrail for Claude Code and Codex. Use this skill only when a task shows clear high-risk token patterns: long-session bloat plus a new complex task, repo-wide or many-file scans, multi-step tool loops, large Bash/Web/tool results likely to flow back into context, repeated long prompt background or rules, excessive MCP/tool exposure, mid-session switching of model or thinking mode or tool strategy, or exhaustive very long output requests. Do not invoke this skill for low or medium risk tasks."
---

# Token Guard

High-risk escalation layer only. Do not use as a default entrypoint.

- Let low and medium risk tasks proceed normally.
- Use this skill only after a lightweight precheck flags high or extreme risk, or when the user explicitly asks for TokenGuard.
- Remove obvious waste before reducing quality.
- Estimate ranges, not fake-precise token counts.

## Session State

- `mode=on` by default. Allowed values: `off`, `on`, `strict`, `relaxed`.
- `budget_k=20` by default. Use only as a reference in the warning output.

Recognize these case-insensitive control commands:

- `TokenGuard: off`
- `TokenGuard: on`
- `TokenGuard: strict`
- `TokenGuard: relaxed`
- `TokenBudget: <N>k`

If the user explicitly says "stop blocking", "stop warning", or "just do it", switch to `relaxed`. If the override is unambiguous, switch to `off`.

## Execution Protocol

### Step 0: Enter or Exit Fast

- Always process TokenGuard control commands before any other gate.
- If the task is not a high-risk escalation case and the user did not explicitly request TokenGuard, do not use this skill.
- If `mode=off` and the current message is not a control command, allow the task and skip the remaining steps.

If the user message is only a TokenGuard control command:

1. Update session state.
2. Reply with a short confirmation.
3. Stop. Do not run risk assessment.

### Step 2: Score Observable Signals

Score each dimension `0`, `1`, `2`, or `3` from explicit evidence only.

| Signal | 0 | 1 | 2 | 3 |
| --- | --- | --- | --- | --- |
| A. Raw input size | short message | moderate text | long text, code, or logs | extremely long context or bundled materials |
| B. Attachments/files | none | small count | many files, images, or a PDF | large multi-file or heavy document analysis |
| C. Task scope | single point or file | small module | multi-file or multi-module | repo-wide or project-wide |
| D. Process complexity | one response | a few tools | multi-step tool use | likely search-read-edit-test-search loop |
| E. Output length | short answer | medium explanation | long analysis | exhaustive or very long output |
| F. Session bloat | fresh session | some history | long session | long session plus a new complex task |
| G. Cache-unfriendly prefix | stable prefix | slight repetition | repeated background or drift | unstable prefix, repeated long rules, or cross-theme pollution |
| H. Tool overhead | minimal tools | simple reads/writes | mixed Bash/Web/MCP/tools | heavy tool exposure or large tool results |
| I. Switch penalty | none | minor style shift | model switch or tool-strategy switch | multiple switches or switching inside a long session |

Apply these minimum penalties when explicit:

- Add at least `2` to `I` for a model switch.
- Add at least `2` to `I` for a thinking-mode switch.
- Add `1-2` to `I` for a tool-strategy or MCP-strategy switch.

### Step 3: Identify Tags

Mark the relevant high-cost tags:
`LONG_SESSION`, `REPO_WIDE`, `TOOL_LOOP`, `BIG_OUTPUT`, `BIG_FILES`, `HEAVY_MCP`, `HEAVY_BASH_WEB`, `SUBAGENT_HEAVY`, `MODEL_SWITCH`, `THINKING_SWITCH`, `TOOL_STRATEGY_SWITCH`.

Mark the relevant waste tags:
`REPEATED_RULES`, `CROSS_THEME`, `FULL_SCAN_FOR_LOCAL_ISSUE`, `OVERSIZED_TOOL_RESULTS`, `UNNEEDED_SUBAGENT`, `OVERLONG_OUTPUT`, `REPEATED_SUMMARY`.

### Step 4: Derive Risk

Add A through I for the total score.

| Total | Risk | Raw size estimate |
| --- | --- | --- |
| 0-5 | minimal | `<2k` |
| 6-9 | small | `2k-8k` |
| 10-14 | medium | `8k-25k` |
| 15-19 | large | `25k-60k` |
| 20+ | extreme | `60k+` |

Derive cache friendliness from `F + G + I`:

- Low penalties and stable continuity -> `high`
- Some drift or history -> `medium`
- Long history, unstable prefix, or major switches -> `low`

Derive effective incremental risk with these rules:

- High raw size plus high cache friendliness -> interpret down by about half a band.
- Medium or high raw size plus low cache friendliness -> keep or raise the final risk.
- `MODEL_SWITCH`, `THINKING_SWITCH`, or `TOOL_STRATEGY_SWITCH` -> bias upward.

Treat the task as `extreme` immediately if any hard trigger is present:

1. Repo-wide or many-file work plus a multi-step tool loop.
2. Many images, a long PDF, or many files requiring item-by-item analysis.
3. A long session continuing with a repo-wide task.
4. A model switch inside a long session while keeping the full prior history.
5. Any two of model switch, thinking switch, and tool-strategy switch.
6. Exhaustive long output across a wide scope.
7. Large Bash, Web, MCP, or tool results likely to keep flowing back into context.
8. Missing key information that would force broad exploratory scanning.

Apply mode adjustments last:

- `strict`: raise by one band.
- `relaxed`: lower by one band.
- Never lower a hard-triggered `extreme` result.

### Step 5: Decide

If the final risk is `large` or `extreme`, intercept immediately.

Do not answer the original task. Output only this warning block:

```markdown
⚠️ Token Guard 拦截

风险等级：[大 / 极高]
缓存友好度：[高 / 中 / 低]
预计有效新增：[中高 / 高 / 极高]
你的 TokenBudget：[<N>k，仅供参考]

主要耗费点（前 3 项）：
1. [...]
2. [...]
3. [...]

已识别风险标签：
[...]

推荐低成本替代方案：
- [...]
- [...]
- [...]

继续方式：
- 回复「继续 / 批准 / ok / proceed」= 仅放行本次原任务
- 或直接给一个缩小范围、降低浪费的新指令
```

Then stop and wait for the next user message.

If the final risk is `minimal`, `small`, or `medium`, execute normally and append one line at the end:

```text
Token Guard：原始体量 ≈ [区间]，缓存友好度 [高/中/低]，预计有效新增 ≈ [区间] tokens（[低/中]风险，粗估）
```

### Step 6: Reassess During Execution

Re-estimate before or after any of these changes:

1. A clear tool loop is about to begin.
2. Large logs, attachments, or files arrive.
3. The user expands scope.
4. The user asks to switch model, thinking mode, tools, or MCP strategy.
5. The answer is growing far beyond the original expectation.

If the task has clearly crossed into `large` or `extreme`, interrupt and re-enter the interception flow.

## Main Cost Drivers

Choose the three most relevant reasons and phrase them concretely:

1. Long session plus a new complex task.
2. Repo-wide or project-wide scanning.
3. Multi-step tool loop.
4. Too many exposed MCP tools.
5. Long Bash, Web, or tool output.
6. Large PDFs, many images, or many files requiring fine-grained review.
7. Exhaustive or overlong output requirements.
8. Long-standing rules repeated in the prompt instead of living in `CLAUDE.md`.
9. Cross-theme contamination inside one session.
10. Local issue, but full-repo analysis requested.
11. Mid-session model switch.
12. Thinking-mode switch.
13. Tool or tool-strategy switch.
14. Subagent usage for a simple task.
15. Repeated summaries of the same history.

## Low-Cost Alternatives

Prefer these first when they do not materially reduce quality:

- Move stable long-lived rules into `CLAUDE.md`.
- Keep only incremental task changes in the current prompt.
- Limit the task to a directory, file set, module, or error surface.
- Compact or restart before continuing a long session.
- Split unrelated themes into separate sessions.
- Start a new session before switching models.
- Avoid changing thinking mode mid-chain.
- Reduce MCP exposure; prefer simple CLI reads when enough.
- Trim tool output before feeding it back into the model.
- Prefer direct grep/read/bash for simple investigation instead of subagents.

Use these second-line reductions when needed:

- Give the conclusion and patch plan first; expand details on demand.
- Sample or prioritize the highest-value subset first.
- Execute in batches with narrower scope.

Avoid suggesting these unless the user explicitly accepts quality tradeoffs:

- Arbitrarily shortening the answer.
- Dropping important constraints.
- Skipping validation, testing, or edge-case checks.

## Topic Drift Check

Classify the current task loosely as one of:

- `code`
- `doc`
- `analysis`
- `image`
- `other`

If the current task is clearly unrelated to the last one or two turns and the session is already long, raise `CROSS_THEME`.

## Output Style

- Stay concise and operational.
- Do not exaggerate or report fake-exact token counts.
- Do not over-block ordinary work.
- When intercepting, never leak the original task answer.
- When allowing, append only the single Token Guard estimate line.

Block only truly expensive patterns: long history, unstable prefix, broad scans, tool loops, heavy tool output, and mid-session switching. Do not add friction to normal work.
