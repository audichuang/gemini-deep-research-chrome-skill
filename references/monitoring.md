# Monitoring Long-Running Gemini Tasks

Use this when Deep Research or Create generation takes minutes.

## Preferred pattern: session twin (mandatory default)

1. Main session starts task and confirms it is running.
2. Spawn a session twin with **`sessions_spawn`** to poll progress.
3. Twin checks Gemini state periodically and exits only when complete/fail.
4. Twin returns concise completion result and key output.
5. Twin must include post-completion handoff prompt (from `post-completion-actions.md`) so main flow can continue with Create/export actions.

Unless user explicitly requests manual polling, treat `sessions_spawn` as required for long-running tasks.

## Twin task template (adapt per topic)

### `sessions_spawn` task template

"Monitor a running Gemini task in Chrome Relay until completion.
1) Use browser tool with profile=openclaw.
2) Snapshot current Gemini tab every 60-120 seconds.
3) Apply completion precedence from completion-signals.md.
4) If relay fails, follow recovery.md, then continue polling.
5) On completion, return:
   - report/title,
   - final summary/conclusion,
   - actionable checklist,
   - source-quality note.
6) If task fails/cancelled, return failure reason + fastest recovery step."

### Behavior rules

- Keep polling until complete/fail (do not stop early).
- Prefer concise progress updates; avoid noisy logs.

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
