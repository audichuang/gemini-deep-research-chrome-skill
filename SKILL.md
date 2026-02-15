---
name: gemini-deep-research-chrome
description: Operate Gemini Deep Research in the user's real Chrome browser via auto-enabled extension relay. Agent has full browser control (open, click, type, navigate, snapshot, monitor). Use when user asks to run Deep Research on Gemini, create audio summaries, export reports, or automate any Gemini web UI task. Always use profile="chrome".
---

# Gemini via Chrome (Full Control)

You have **complete control** over the user's real Chrome browser via an auto-enabled extension relay. The extension automatically attaches to all tabs, auto-reconnects on failure, and requires zero user interaction. You can open pages, click buttons, type text, take snapshots, and navigate freely.

## Critical rules

1. **Always use `profile="chrome"`** — this is the user's real Chrome with login sessions.
2. **Never use `profile="openclaw"`** — that launches a separate Chromium instance without the user's sessions, bookmarks, or extensions. If you use openclaw, you are operating the WRONG browser.
3. **Never ask the user to click the extension icon or manually enable relay** — the extension is always on.

---

## Core workflow

1. Verify Chrome relay is live.
2. Navigate to Gemini + enable Deep Research.
3. Submit prompt and start research.
4. Monitor until done.
5. Return results and offer next actions.

---

## 1) Verify relay

1. Run `browser status` with `profile="chrome"`.
2. Run `browser tabs` with `profile="chrome"`.
3. Look for a Gemini tab (`gemini.google.com`).
4. If no Gemini tab exists, **navigate to gemini.google.com yourself** — do not ask the user to open it. Use `browser` tool with `action="open"` and `targetUrl="https://gemini.google.com"`, or use an existing tab and navigate via the address bar.
5. If `open` action returns an error (e.g. 404), use an existing `about:blank` tab and navigate it to `https://gemini.google.com` using CDP `Page.navigate`.

If `browser status` shows the relay is not ready, **wait 5 seconds and retry** — the extension auto-reconnects. Only after 3 failed retries should you follow the recovery flow in `references/recovery.md`.

---

## 2) Deep Research execution

1. Click **工具**.
2. Enable **Deep Research**.
3. Verify Deep Research is active (look for the chip/badge).
4. Submit the user's prompt into the text input.
5. If a plan card appears with **開始研究**, click it.

> **Important**: plan generation ≠ execution. If **開始研究** is present, the task has NOT started yet — you must click it.

---

## 3) Create features execution

Use the **建立** menu when user asks for derivative output from a completed report:

* 建立網頁
* 建立資訊圖表
* 建立測驗
* 建立學習卡
* 建立語音摘要

For **建立語音摘要**:

1. Click menu item.
2. Fill description/prompt.
3. Submit generation.
4. Monitor until completion.
5. Download exported audio file from Gemini (分享及匯出 / download entry).
6. Send audio file to user in chat.
7. Return status only after file delivery.

---

## 4) Monitor long tasks

For long-running jobs, default to **`sessions_spawn`** (session twin) to monitor until completion.

* Spawn twin right after task starts running.
* Twin must poll until done/fail and return concise result.
* Do not rely on manual user follow-up.

Use implementation template in `references/monitoring.md`.
Use completion signals and precedence in `references/completion-signals.md`.

---

## 5) Return output to user

When done, return:

1. short actionable summary first,
2. key conclusion and market stage (if research report),
3. actionable checklist,
4. source-quality note when references are mixed quality.

Then immediately run post-completion handoff flow in `references/post-completion-actions.md`:

* provide one compact next-action menu,
* default suggest **建立語音摘要** if user has no preference,
* execute selected Create/export action directly.

---

## 6) Do not (anti-patterns)

* **Do not use `profile="openclaw"`** — this is the #1 mistake. It opens a different browser.
* Do not ask the user to click any extension button or manually enable relay.
* Do not assume "plan shown" means "research running".
* Do not judge completion from one weak signal only.
* Do not skip `sessions_spawn` for long-running tasks unless user explicitly asks.
* Do not dump full long report before giving concise summary.

---

## References

* `references/quick-run.md` — 1-minute operator checklist (fast path)
* `references/recovery.md` — relay recovery (mostly automatic)
* `references/monitoring.md` — session twin and optional cron monitoring template
* `references/completion-signals.md` — completion criteria and precedence
* `references/post-completion-actions.md` — required follow-up actions after research completes
