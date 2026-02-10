# Completion Signals and Precedence

Use multiple signals. Do not rely on one weak indicator.

## Strong completion signals (highest confidence)

1. Research/generation card shows explicit **已完成**.
2. Gemini message explicitly says research/generation is complete.
3. Final report panel is visible with populated content.

## Medium signals

4. Stop button disappears and normal send state returns.
5. Create output card/status appears as done/ready.

## Weak signals (never use alone)

6. Time elapsed is long enough.
7. UI looks idle without explicit complete markers.

## Precedence rule

Decide status with this order:

1. If any strong signal appears → mark complete.
2. Else if only medium signals appear → keep checking once more before declaring complete.
3. If only weak signals appear → keep monitoring.

## Failure signals

- Explicit error/failure message in Gemini task card.
- User-cancel action acknowledged by UI.

If failure detected, return:
- what failed,
- current state,
- fastest recovery step.
