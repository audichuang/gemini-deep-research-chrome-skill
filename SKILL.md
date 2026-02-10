---
name: gemini-deep-research-chrome
description: Operate Gemini in the user’s real Chrome via OpenClaw Browser Relay (profile=chrome): run Deep Research, monitor progress, read/export reports, and use Create features such as audio summaries. Use when user asks you to control Gemini directly in their Chrome tab.
---

# Gemini via Chrome Relay

Follow this workflow to operate Gemini reliably in the user’s real Chrome tab.

## Core workflow

1. Connect relay and stabilize tab.
2. Enable Deep Research (or user-requested Create flow).
3. Execute task.
4. Monitor until done.
5. Extract and return useful result.

---

## 1) Connect and stabilize relay

1. Always use browser tool with `profile="chrome"` (never `openclaw` for this workflow).
2. Run `browser status` and `browser tabs`.
3. Find Gemini tab URL (`gemini.google.com`).
4. If no Gemini tab appears, ask user to:
   - switch to Gemini tab,
   - click OpenClaw Browser Relay toolbar button (ON).

If `tab not found`, use recovery order in `references/recovery.md`.

---

## 2) Deep Research execution

1. Click **工具**.
2. Enable **Deep Research**.
3. Verify Deep Research is active (placeholder/chip).
4. Submit user prompt.
5. If plan card appears with **開始研究**, click it.

Important: plan generation is not execution. If **開始研究** is present, task has not started yet.

---

## 3) Create features execution

Use **建立** menu when user asks for derivative output from report/content:

- 建立網頁
- 建立資訊圖表
- 建立測驗
- 建立學習卡
- 建立語音摘要

For **建立語音摘要**:
1. Click menu item.
2. Fill description/prompt.
3. Submit generation.
4. Monitor until completion.
5. Return status + how user can play/share.

---

## 4) Monitor long tasks

For long-running jobs, default to **`sessions_spawn`** (session twin) to monitor until completion.

- Do not rely on manual user follow-up for long runs.
- Spawn twin right after task starts running.
- Twin must poll until done/fail and return concise result.

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
- provide one compact next-action menu,
- default suggest **建立語音摘要** if user has no preference,
- execute selected Create/export action directly.

---

## 6) Do not (anti-patterns)

- Do not switch to non-chrome profile for this task.
- Do not assume “plan shown” means “research running”.
- Do not judge completion from one weak signal only.
- Do not repeatedly ask user to reattach relay before trying recovery sequence.
- Do not skip `sessions_spawn` for long-running Deep Research/Create jobs unless user explicitly asks for manual polling.
- Do not dump full long report before giving concise summary.

---

## References

- `references/quick-run.md` — 1-minute operator checklist (fast path)
- `references/recovery.md` — relay recovery order and failure handling
- `references/monitoring.md` — session twin and optional cron monitoring template
- `references/completion-signals.md` — completion criteria and precedence
- `references/post-completion-actions.md` — required follow-up actions after research completes
