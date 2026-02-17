# Recovery (Connection Failures)

使用 profile="openclaw" 時非常穩定，幾乎不需要 recovery。如果遇到問題：

## Tab or connection error

1. Run `browser status` and `browser tabs` again.
2. If tabs appear, pick the Gemini tab and continue.
3. If no tabs appear, wait 5 seconds and retry (up to 3 times).
4. If all else fails, navigate to `https://gemini.google.com` in a new tab.

## Reliability notes

- Do NOT ask the user to open tabs or click any buttons — handle everything yourself.
- After successful recovery, continue without asking repeated confirmations.
