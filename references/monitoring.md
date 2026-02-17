# Monitoring Long-Running Gemini Tasks

Use this when Deep Research or Create generation takes minutes.

## Preferred pattern: session twin (mandatory for Deep Research)

Deep Research 需要 5-15 分钟，**必须**使用 `sessions_spawn`。

### 流程

```
Phase 1: 主代理
├── 1. 启动 Deep Research
├── 2. 确认「開始研究」已点击，任务运行中
└── 3. sessions_spawn 委派「等待子代理」

Phase 2: 等待子代理
├── 1. 每 60-120 秒 snapshot 检查进度
├── 2. 使用 completion-signals.md 判断完成
└── 3. 返回「已完成，可获取分享连结」

Phase 3: 主代理（收到子代理结果后）
├── 1. 获取新 snapshot
├── 2. 点击「分享及匯出」
└── 3. 返回分享连结给用户
```

## Twin task template

### `sessions_spawn` task template

"在 Chrome 中监控 Gemini Deep Research 任务直到完成。

1) 使用 browser 工具，profile=\"chrome\"
2) 每 60-120 秒获取一次 snapshot
3) 使用 completion-signals.md 判断是否完成
4) 如果收到 'Element not found' 错误 → 立即获取新 snapshot 后重试
5) 如果 relay 断连 → 按照 recovery.md 恢复后继续监控

完成条件（使用 completion-signals.md 判断）：
- 出现「已完成」标志
- 或 Gemini 明确显示「研究完成」

完成后返回：
- 研究报告标题
- 一句话总结
- 告诉用户「可以获取分享连结」"

### Behavior rules

- 必须先获取新 snapshot 才能执行任何 act
- 每次 act 后如果页面有变化，必须再 snapshot
- 如果收到任何错误，先 snapshot 再重试
- 不要在子代理中尝试获取分享连结 — 那属于 Phase 3

## Optional cron pattern

Use cron only when user explicitly wants scheduled reminders/check-ins.

Suggested reminder text style:
- Mention this is a reminder.
- Include topic context.
- Include expected action (e.g., open report, review output, decide next step).

## Failure handling in monitoring

If polling hits relay errors repeatedly:
1. run recovery flow (`recovery.md`),
2. continue polling after recovery,
3. do not mark failed unless task itself fails or user stops it.
