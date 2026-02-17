---
name: gemini-deep-research-chrome
description: "Operate Gemini Deep Research via browser tool: run Deep Research, monitor progress, read/export reports, and use Create features such as audio summaries. Agent has full browser control to open, navigate, click, and type autonomously."
---

# Gemini Deep Research

Follow this workflow to operate Gemini Deep Research via the browser tool. You have full browser control — open pages, click, type, navigate, and take snapshots autonomously without requiring any user action.

## Core workflow

1. Open Gemini (or find existing tab).
2. Enable Deep Research (or user-requested Create flow).
3. Execute task.
4. Monitor until done.
5. Extract and return useful result.

---

## 1) Open Gemini

1. Run `browser status` and `browser tabs`.
2. Look for an existing Gemini tab (`gemini.google.com`).
3. If no Gemini tab exists, **open one yourself** — navigate to `https://gemini.google.com`.
4. Do NOT ask the user to open tabs or click any buttons.

If connection issues occur, follow `references/recovery.md`.

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
5. Download exported audio file from Gemini (分享及匯出 / download entry).
6. Send audio file to user in chat.
7. Return status only after file delivery.

---

## 4) Monitor long tasks

For long-running jobs, default to **`sessions_spawn`** (session twin) to monitor until completion.

- Spawn twin right after task starts running.
- Twin must poll until done/fail and return concise result.
- Do not rely on manual user follow-up for long runs.

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

- Do not assume "plan shown" means "research running".
- Do not judge completion from one weak signal only.
- Do not ask the user to manually open tabs, click buttons, or enable any relay.
- Do not skip `sessions_spawn` for long-running Deep Research/Create jobs unless user explicitly asks for manual polling.
- Do not dump full long report before giving concise summary.

---

## References

- `references/quick-run.md` — 1-minute operator checklist (fast path)
- `references/recovery.md` — relay recovery order and failure handling
- `references/monitoring.md` — session twin and optional cron monitoring template
- `references/completion-signals.md` — completion criteria and precedence
- `references/post-completion-actions.md` — required follow-up actions after research completes
