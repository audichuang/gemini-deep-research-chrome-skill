# Recovery (Relay / Tab Failures)

Use this exact order when operation fails.

## `tab not found` with visible Gemini tab

1. Refresh tabs and pick newest Gemini `targetId`.
2. Retry snapshot/action on new target.
3. If still failing, ask user to re-attach Relay on current Gemini tab.
4. Retry once.
5. If still failing, restart gateway (`openclaw gateway restart`).
6. Ask user to re-attach Relay again, then continue.

## Tabs list is empty after gateway restart

This is expected until user re-attaches Relay.

Prompt user minimally:
1. Open Gemini tab in Chrome.
2. Click OpenClaw Browser Relay button (ON).
3. Reply "好了".

Then resume automatically.

## Reliability notes

- Use one minimal user action request at a time.
- After successful recovery, continue without asking repeated confirmations.
- Prefer re-discovering current targetId instead of reusing stale IDs.
