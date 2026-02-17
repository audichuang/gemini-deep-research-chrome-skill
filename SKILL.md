---
name: gemini-deep-research-chrome
description: "Operate Gemini Deep Research via browser tool: run Deep Research, monitor progress, read/export reports, and use Create features such as audio summaries. Agent has full browser control — always use profile='openclaw'."
---

# Gemini Deep Research

Always use `profile="openclaw"` — this is the stable, OpenClaw-managed browser that works reliably without any extension or relay issues.

## Core workflow

1. Use `profile="openclaw"` (always, no exceptions)
2. Open Gemini (or find existing tab)
3. Enable Deep Research (or user-requested Create flow)
4. Execute task
5. Monitor until done
6. Extract and return useful result

**重要**：使用 openclaw profile 時，一切都非常穩定，不需要重啟、不需要重新 snapshot、也不會有「Session not found」錯誤。

---

## 1) Open Gemini

1. Run `browser status profile=openclaw`.
2. Run `browser tabs`.
3. Look for an existing Gemini tab (`gemini.google.com`).
4. If no Gemini tab exists, **open one yourself** — navigate to `https://gemini.google.com`.
5. Do NOT ask the user to open tabs or click any buttons.

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

- Do not use `profile="chrome"` — always use `profile="openclaw"` for stability
- Do not assume "plan shown" means "research running"
- Do not judge completion from one weak signal only
- Do not ask the user to manually open tabs, click buttons, or enable any relay
- Do not skip `sessions_spawn` for long-running Deep Research/Create jobs unless user explicitly asks for manual polling
- Do not dump full long report before giving concise summary
- Do not restart gateway or browser — it's not needed with openclaw profile
- Do not re-snapshot unnecessarily — the session is stable
