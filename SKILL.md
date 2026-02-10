---
name: gemini-deep-research-chrome
description: "Operate Gemini in the user’s real Chrome via OpenClaw Browser Relay (profile=chrome): attach tab, open/switch Gemini, run Deep Research, start plan, monitor until done, read/export report, use Create features (webpage, infographic, quiz, flashcards, audio summary), and recover relay failures. Use when user asks to control Gemini for research/content generation."
---

# Gemini via Chrome Relay (Deep Research + Create)

Use this skill when the user wants you to operate Gemini directly in their real Chrome tab.

## 1) Connect and stabilize relay

1. Run `browser status` + `browser tabs` with `profile="chrome"`.
2. Find Gemini tab URL (`gemini.google.com`).
3. If no Gemini tab appears, ask user to:
   - switch to Gemini tab,
   - click OpenClaw Browser Relay toolbar button (ON).

### Recovery for `tab not found`

Use this exact order:
1. refresh tabs and retry with newest Gemini `targetId`;
2. ask user to re-attach Relay on current Gemini tab;
3. if still broken, restart gateway (`openclaw gateway restart`) and re-attach.

---

## 2) Start Deep Research correctly

1. In compose area, click **工具**.
2. Enable **Deep Research**.
3. Verify Deep Research is active (research placeholder/chip visible).
4. Paste user research prompt and submit.

### Critical nuance
Deep Research often returns a **plan card** first. If button **開始研究** appears, click it.
Without this click, research may never actually run.

---

## 3) Monitor long-running jobs (session twin pattern)

Deep Research can take many minutes. Do not force user to keep asking.

### Preferred monitoring pattern

1. Main flow:
   - trigger task,
   - confirm “running”.
2. Spawn a session twin (`sessions_spawn`) for polling/checking progress.
3. Twin periodically snapshots Gemini and checks state:
   - running: stop button / in-progress indicators;
   - done: “已完成” + report panel/card visible.
4. Twin returns completion summary when done.

### Optional timed reminders/checks

When user wants scheduled follow-up, add `cron` checks/reminders instead of manual pinging.
Use reminder text that clearly says it is a reminder and references the research topic.

---

## 4) Read report and return useful output

When report completes:

1. Open/keep report panel on the right.
2. Extract:
   - executive summary,
   - market-stage judgment / final stance,
   - actionable checklist,
   - key tables,
   - source list quality.
3. Reply in two layers:
   - short actionable summary first,
   - deeper analysis on request.
4. If sources are mixed quality (blogs/media-heavy), explicitly add a risk note.

---

## 5) Use Gemini “建立” features (not only Deep Research)

From report panel top controls, click **建立** and use:

- **建立網頁**: turn report into webpage/app draft.
- **建立資訊圖表**: generate infographic.
- **建立測驗**: create quiz.
- **建立學習卡**: create flashcards.
- **建立語音摘要**: generate spoken/audio-style summary from report.

For **建立語音摘要**:
1. click menu item;
2. fill prompt/description textbox;
3. submit and monitor generation;
4. return result status + how to play/share.

---

## 6) Other high-value controls to check

In report header/tools, also verify and use when needed:

- **目錄選單** (section navigation)
- **分享及匯出** (share/export)
- **資料來源區塊** (used vs reviewed-not-used sources)
- **新對話 / 我的內容 / Gem / 設定與說明**

---

## 7) Communication style

1. Confirm concrete action (“已幫你按開始研究”).
2. If blocked, ask for one minimal user action only.
3. After recovery, continue automatically.
4. Avoid repeated back-and-forth if issue is relay-side.

---

## 8) Pitfalls checklist

- `tab not found` with visible tab list → stale targetId/relay link.
- Plan exists but no progress → forgot to click **開始研究**.
- Relay reset after gateway restart → tabs empty until re-attach.
- Large report snapshots truncate → summarize visible core first, then drill down.
