## Scenario

Allowed turn that finishes normally, but final actual usage ends up at least two bands above the baseline estimate.

## Input

```text
Use $token-guard to assess this request before doing it:
"Investigate this moderate bug report, inspect a few files, and give me a concise fix plan."
```

## Expected assessment

- Initial risk stays `medium`.
- Token Guard allows the task.
- The turn records a local baseline such as `expected_incremental_band=band 1`.

## Execution observations

- The investigation stays within allowed scope and does not cross into `large` or `extreme`.
- Real usage grows because tool results are larger than expected, or the answer needs more synthesis than the initial estimate assumed.
- Final actual incremental usage lands around `band 3`.

## Expected output shape

```text
[normal task answer]

Token Guard：原始体量 ≈ 8k-25k，缓存友好度 中，预计有效新增 ≈ 2k-8k tokens（中风险，粗估）
Token Guard 偏差：实际有效新增 ≈ 25k-60k，较事前粗估高 2+ 档；主要原因：工具结果体量意外膨胀
```

## Notes

- The task still completes.
- Emit exactly one drift line after the standard estimate line.
- Name one dominant cause only.
