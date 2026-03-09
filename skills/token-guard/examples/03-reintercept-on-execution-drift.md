## Scenario

Allowed turn that becomes too expensive during execution and must re-enter the interception flow.

## Input

```text
Use $token-guard to assess this request before doing it:
"Start with this local issue, inspect the relevant code, and tell me what to change."
```

## Expected assessment

- Initial risk stays `medium`.
- Token Guard allows the task with a local baseline such as `expected_incremental_band=band 1` or `band 2`.

## Execution observations

- Scope expands from local analysis into a multi-step repo scan.
- Large tool results keep flowing back into context.
- Actual incremental usage crosses into `large` or `extreme`.

## Expected output shape

```markdown
⚠️ Token Guard 拦截

风险等级：[大 / 极高]
缓存友好度：[高 / 中 / 低]
预计有效新增：[中高 / 高 / 极高]
你的 TokenBudget：[20k，仅供参考]

主要耗费点（前 3 项）：
1. 多步工具循环持续扩张
2. 大量工具结果回流到上下文
3. 范围从局部问题扩大到多文件

已识别风险标签：
[TOOL_LOOP, HEAVY_BASH_WEB, BIG_OUTPUT]

推荐低成本替代方案：
- 先限制到单个目录或错误面
- 先给结论和补丁计划，再按需展开
- 分批执行，不要一次性全量扫描

继续方式：
- 回复「继续 / 批准 / ok / proceed」= 仅放行本次原任务
- 或直接给一个缩小范围、降低浪费的新指令
```

## Notes

- Reuse the existing interception block.
- Do not emit the final normal answer.
- Do not append `Token Guard 偏差` after an in-flight interception.
