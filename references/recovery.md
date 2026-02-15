# Recovery (Relay Failures)

The Chrome extension auto-reconnects every 3 seconds when the relay drops. Most failures resolve themselves within seconds with no user action required.

## Standard recovery (automatic)

1. Wait 5 seconds and retry `browser status` + `browser tabs` with `profile="chrome"`.
2. If tabs appear, continue normally.
3. If still no connection after 3 retries (~15 seconds), proceed to manual recovery below.

## Manual recovery (rare)

Only use this if auto-reconnect fails after 15+ seconds:

1. Ask user to restart gateway: `openclaw gateway restart`.
2. Wait 10 seconds for the extension to auto-reconnect.
3. Run `browser tabs` with `profile="chrome"` again.
4. If tabs appear, continue. If not, ask user to refresh the Gemini tab in Chrome.

## Important

* **Never switch to `profile="openclaw"`** as a recovery step — that opens a different browser.
* **Never ask the user to click the extension icon** — the extension is always auto-enabled.
* After successful recovery, continue without repeated confirmations.
* Prefer re-discovering current targetId instead of reusing stale IDs.
