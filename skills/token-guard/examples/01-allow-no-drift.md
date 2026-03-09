## Scenario

Allowed turn with no meaningful drift.

## Input

```text
Use $token-guard to assess this request before doing it:
"Review this small diff, point out one bug if any, and keep the answer short."
```

## Expected assessment

- Initial risk stays `small` or `medium`.
- Token Guard allows the task.
- The turn records a local baseline such as `expected_incremental_band=band 1`.

## Execution observations

- No broad scope expansion.
- No large tool output.
- No model, thinking, or tool-strategy switch.
- Final actual incremental usage stays within the same band, or only one band higher.

## Expected output shape

```text
[normal task answer]

Token Guard：原始体量 ≈ 2k-8k，缓存友好度 高，预计有效新增 ≈ 2k-8k tokens（低风险，粗估）
```

## Notes

- Do not emit `Token Guard 偏差`.
- A one-band gap is treated as normal estimation noise.
