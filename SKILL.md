---
name: gemini-deep-research-chrome
description: Operate Gemini in the user’s real Chrome via OpenClaw Browser Relay (profile=chrome), including: attach tab, open/switch Gemini pages, enable Deep Research, start research plan, monitor progress, read final report, and recover from tab-not-found relay failures. Use when user asks to control Gemini/Deep Research for them.
---

# Gemini Deep Research via Chrome Relay

Use this workflow to reliably operate Gemini in the user’s real Chrome tab.

## 1) Connect to Chrome Relay first

1. Use `browser status` and `browser tabs` with `profile="chrome"`.
2. Confirm a Gemini tab exists (`https://gemini.google.com/...`).
3. If no Gemini tab appears, ask user to:
   - switch to the Gemini tab,
   - click OpenClaw Browser Relay toolbar button (ON).

## 2) Stabilize target tab

1. Prefer current Gemini tab from `browser tabs`.
2. If `snapshot` returns `tab not found`:
   - refresh tab list and try newest Gemini `targetId`.
   - if still broken, ask user to re-attach Relay on current Gemini tab.
   - if service is stuck, restart gateway (`openclaw gateway restart`) and re-attach.

## 3) Enter Deep Research mode

Inside Gemini compose area:

1. Click **工具 (Tools)**.
2. Enable **Deep Research**.
3. Verify state by either:
   - compose placeholder changes to research-style text (e.g.「你想研究什麼？」), or
   - Deep Research chip appears and is cancellable.

## 4) Submit research request correctly

1. Type user’s research prompt in Traditional Chinese when requested.
2. Submit the message.
3. If Gemini returns a **research plan card** with **開始研究**, click it.

Important: Deep Research may stop at “plan ready” until **開始研究** is pressed.

## 5) Monitor run state

Treat as running when UI shows signs like:
- stop button (e.g.「停止回覆」),
- in-progress status on research card.

Treat as completed when UI shows:
- completed state (e.g.「已完成」),
- message indicating research is done,
- report panel visible on the right.

## 6) Read and extract final report

After completion:

1. Open/keep right-side report panel.
2. Capture:
   - report title,
   - executive summary / key conclusions,
   - market-stage judgment,
   - actionable checklist,
   - cited sources quality.
3. Return a concise summary to user first, then offer deeper breakdown.

## 7) Open a new Gemini page and discover features

When user asks to explore more functions:

1. Open new Gemini URL in Chrome profile.
2. Use left menu and top controls to locate:
   - 新對話,
   - 我的內容,
   - Gem,
   - 設定與說明,
   - 分享及匯出,
   - 建立,
   - 資料來源區塊.
3. Explain each feature in practical terms (what user can do next).

## 8) Communication style for this workflow

1. Confirm action taken (“已幫你按開始研究”).
2. If blocked, provide one minimal step for user (not many).
3. When recovered, continue automatically and avoid re-asking repeated steps.
4. Add a risk note when sources are mixed quality (media/blog vs primary filings).

## 9) Known pitfalls checklist

- `tab not found` even though tab exists: stale targetId or broken relay link.
- Gemini plan generated but not running: forgot to press **開始研究**.
- Relay reset after gateway restart: tabs list empty until user re-attaches tab.
- Big report snapshots truncate: summarize visible core conclusions first.
