# Monitoring Long-Running Gemini Tasks

Use this when Deep Research or Create generation takes minutes.

## Preferred pattern: session twin

1. Main session starts task and confirms it is running.
2. Spawn a session twin (`sessions_spawn`) to poll progress.
3. Twin checks Gemini state periodically and exits only when complete/fail.
4. Twin returns concise completion result and key output.

## Twin task template (adapt per topic)

- Open current Gemini tab via Chrome relay.
- Snapshot every 60-120s.
- Apply completion precedence from `completion-signals.md`.
- On completion:
  - capture title,
  - capture final summary/conclusion,
  - capture actionable items,
  - capture source-quality note.

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
