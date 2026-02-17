# Recovery (Connection Failures)

Use this order when browser operations fail.

## Tab or connection error

1. Run `browser status` and `browser tabs` again.
2. If tabs appear, pick the Gemini tab and continue.
3. If no tabs appear, wait 5 seconds and retry (up to 3 times).
4. If still no connection, restart gateway (`openclaw gateway restart`), wait 10 seconds, then retry.
5. If all else fails, navigate to `https://gemini.google.com` in a new tab.

## Reliability notes

- Do NOT ask the user to open tabs or click any buttons — handle everything yourself.
- After successful recovery, continue without asking repeated confirmations.
- Prefer re-discovering current targetId instead of reusing stale IDs.
