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

```
Phase 1: 主代理 - 启动研究
├── 1. Verify Chrome relay is live
├── 2. Navigate to Gemini + enable Deep Research
├── 3. Submit prompt
├── 4. 点击「開始研究」确认任务启动
└── 5. sessions_spawn 委派「等待完成子代理」

Phase 2: 等待子代理 (sessions_spawn)
├── 1. 每 60-120 秒 snapshot 检查进度
├── 2. 根据 completion-signals.md 判断完成
└── 3. 返回「已完成，可获取分享链接」

Phase 3: 主代理 - 获取分享链接
├── 1. 收到子代理结果
├── 2. 获取新 snapshot
├── 3. 点击「分享及匯出」获取链接
└── 4. 返回给用户
```

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

## 3) Monitor long tasks (必须使用子代理)

Deep Research 需要 5-15 分钟，**必须**使用 `sessions_spawn` 委派子代理等待。

### 流程

```
主代理：
1. 确认「開始研究」已点击，任务开始运行
2. 立即 sessions_spawn 委派「等待完成子代理」
3. 告知用户「研究进行中，完成後會自動通知」

等待子代理：
1. 每 60-120 秒获取 snapshot 检查进度
2. 使用 completion-signals.md 判断完成
3. 完成後返回「研究报告已完成」
```

### 子代理模板

```
sessions_spawn task:"在 Chrome 中监控 Gemini Deep Research 任务直到完成。

1) 使用 browser 工具，profile=\"chrome\"
2) 每 60-120 秒获取一次 snapshot
3) 使用 completion-signals.md 判断是否完成
4) 如果收到 'Element not found' 错误 → 立即获取新 snapshot 后重试
5) 如果 relay 断连 → 按照 recovery.md 恢复后继续监控

完成条件（使用 completion-signals.md 判断）：
- 出现「已完成」标志
- 或 Gemini 明确显示「研究完成」

完成后返回：
- 研究报告标题
- 一句话总结
- 告诉用户「可以获取分享连结」"

label:"等待 Gemini 研究完成"
```

---

## 4) Get share link (Phase 3)

收到子代理「完成」返回后：

1. **获取新 snapshot** — 必须先获取新 DOM
2. 点击「分享及匯出」按钮
3. 点击「公開分享連結」
4. 复制链接返回给用户

---

## 5) Browser Tool 正确用法

### act action 的 kind 参数格式

| kind | 参数格式 | 用途 |
|------|----------|------|
| click | `{ref: "e123"}` | 点击按钮/链接 |
| type | `{ref: "e123", text: "hello"}` | 输入文本到单个字段 |
| fill | `{fields: [{ref: "e1", value: "val1"}, {ref: "e2", value: "val2"}]}` | 批量填充多个表单字段 |
| select | `{ref: "e123", values: ["option1"]}` | 下拉选单 |
| press | `{ref: "e123", key: "Enter"}` | 模拟按键 |

### ⚠️ 常见错误

| 错误 | 原因 | 正确写法 |
|------|------|----------|
| `fields are required` | `kind: "fill"` 用了 `ref` + `text` | 用 `kind: "type"` |
| `Element not found` | 使用了过期 snapshot 的 element ref | 每次 act 前先 snapshot |

### 关键原则

1. **每次 act 前必须先 snapshot** — 永远不要使用超过 30 秒前的 element reference
2. **页面刷新后立即 snapshot** — 任何导航、点击后都要重新获取 DOM
3. **如果收到 "Element not found"** → 立即获取新 snapshot 后重试

---

## 6) Error handling

### Element not found

**原因**：使用了过期 snapshot 中的 element reference

**解决**：
1. 立即执行 `browser snapshot` 获取新 DOM
2. 从新 snapshot 中获取正确的 ref
3. 重试操作

### fields are required

**原因**：kind 参数格式错误

**解决**：检查上方「Browser Tool 正确用法」表格

### Can't reach the OpenClaw browser control service

**原因**：Gateway 或 Extension 重连

**解决**：
1. 等待 5 秒
2. 重试 `browser status`
3. 如果持续失败，告知用户手动重启

---

## 7) Create features execution

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

## 8) Return output to user

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

## 9) Do not (anti-patterns)

* **Do not use `profile="openclaw"`** — this is the #1 mistake. It opens a different browser.
* **Do not use old element references** — always snapshot before each act. This causes "Element not found" errors.
* **Do not use wrong kind parameters** — fill requires `fields: []`, not `ref` + `text`. This causes "fields are required" errors.
* Do not ask the user to click any extension button or manually enable relay.
* Do not assume "plan shown" means "research running" — you must click 「開始研究」.
* Do not judge completion from one weak signal only.
* Do not skip `sessions_spawn` for long-running tasks.
* Do not dump full long report before giving concise summary.

---

## References

* `references/quick-run.md` — 1-minute operator checklist (fast path)
* `references/recovery.md` — relay recovery (mostly automatic)
* `references/monitoring.md` — session twin template
* `references/completion-signals.md` — completion criteria and precedence
* `references/post-completion-actions.md` — required follow-up actions after research completes
