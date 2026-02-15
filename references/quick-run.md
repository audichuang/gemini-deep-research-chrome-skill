# Quick Run (Operator Mode)

Use this as a fast-path checklist. All steps use `profile="chrome"`.

## A) Deep Research fast path

1. `browser status` + `browser tabs` with `profile="chrome"`.
2. Find or open Gemini tab (`gemini.google.com`).
3. Click **工具** → enable **Deep Research**.
4. Paste user prompt and submit.
5. If **開始研究** appears, click it.
6. Monitor until strong completion signal appears (see `completion-signals.md`).
7. Return:
   * 5-10 bullet summary,
   * final stance,
   * action list,
   * source-quality note.
8. Immediately offer next-action menu:
   * 建立語音摘要（default）
   * 建立資訊圖表
   * 建立測驗
   * 建立學習卡
   * 建立網頁
   * 分享及匯出

## B) Create 語音摘要 fast path

1. Ensure report/content panel is open.
2. Click **建立** → **建立語音摘要**.
3. Fill prompt and submit.
4. Monitor until done.
5. Download audio file from Gemini export/share entry.
6. Send audio file to user in chat.
7. Confirm delivery and offer next action.

## C) If broken

* Wait 5 seconds and retry — the extension auto-reconnects.
* After 3 failed retries, follow `recovery.md`.
* **Never switch to `profile="openclaw"`**.
