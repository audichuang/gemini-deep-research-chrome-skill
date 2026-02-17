---
name: gemini-deep-research-chrome
description: "Operate Gemini Deep Research via browser tool: run Deep Research, monitor progress, read/export reports, and use Create features such as audio summaries. Agent has full browser control — always use profile='openclaw'."
---

# Gemini Deep Research

Always use `profile="openclaw"` — this is the stable, OpenClaw-managed browser that works reliably without any extension or relay issues.

## Core workflow

```
Phase 1: 主代理 - 启动研究
├── 1. 使用 profile="openclaw"
├── 2. 打开 Gemini + 启用 Deep Research
├── 3. 输入 prompt
├── 4. 点击「開始研究」确认任务启动
└── 5. sessions_spawn 委派「等待完成子代理」

Phase 2: 等待子代理 (sessions_spawn)
├── 1. 每 60-120 秒 snapshot 检查进度
├── 2. 根据 Completion Signals 判断完成
└── 3. 返回「已完成，可获取分享链接」

Phase 3: 主代理 - 自动获取分享链接
├── 1. 收到子代理结果
├── 2. 获取新 snapshot
├── 3. 点击「分享及匯出」获取链接
└── 4. 返回给用户
```

---

## 1) Open Gemini

1. Run `browser status profile=openclaw`.
2. Run `browser tabs`.
3. Look for an existing Gemini tab (`gemini.google.com`).
4. If no Gemini tab exists, **open one yourself** — navigate to `https://gemini.google.com`.
5. Do NOT ask the user to open tabs or click any buttons.

**⚠️ 每次操作前必須先 snapshot！** Gemini 頁面的 element ref 每次都會變，絕對不要使用超過 30 秒前的 element ref。

**💡 省 token 技巧**：使用 `selector` 只取特定元素，不用取整頁：
```javascript
// 只取輸入框區域
browser snapshot selector="textarea"

// 只取特定按鈕
browser snapshot selector="button:has-text('開始研究')"

// 取對話區域
browser snapshot selector="main"
```

---

## 2) Deep Research execution

1. Click **工具**.
2. Enable **Deep Research**.
3. Verify Deep Research is active (placeholder/chip).
4. Submit user prompt.
5. If plan card appears with **開始研究**, click it.

Important: plan generation is not execution. If **開始研究** is present, task has not started yet.

---

## 3) Monitor long tasks (必须使用子代理)

Deep Research 需要數分鐘到數十分鐘，**必须**使用 `sessions_spawn` 委派子代理等待。

### 流程

```
主代理：
1. 确认「開始研究」已点击，任务开始运行
2. 立即 sessions_spawn 委派「等待完成子代理」
3. 告知用户「研究进行中，完成後會自動通知」

等待子代理：
1. 每 60-120 秒获取 snapshot 检查进度
2. 使用下方 Completion Signals 判断完成
3. 完成后返回「研究报告已完成」
```

### 子代理模板

```
sessions_spawn task:"在 Chrome 中监控 Gemini Deep Research 任务直到完成。

1) 使用 browser 工具，profile=\"openclaw\"
2) 每 60 秒获取一次 snapshot（必须先 snapshot 才能获取最新的 element ref！）
   - 省 token 技巧：用 selector=\"main\" 只取主要对话区
3) 使用下方 Completion Signals 判断是否完成

⚠️ 重要：每次 click 或 act 操作前，必須先執行 browser snapshot 取得最新的 element ref！
千萬不要使用舊的 ref，否則會出現 'Element not found' 錯誤。

完成条件（参见下方 Completion Signals）：
- 出现「已完成」标志
- 或 Gemini 明确显示「研究完成」
- 或出现「分享及匯出」按钮

完成后返回：
- 研究报告标题
- 一句话总结
- 告诉用户「可以获取分享连结」"

label:"等待 Gemini 研究完成"
```

### Completion Signals (判断完成)

**强烈信号（高可信度）**：
1. 研究/生成卡片显示明确的「已完成」
2. Gemini 消息明确表示研究/生成完成
3. 最终报告面板可见且有内容

**中等信号**：
4. 停止按钮消失， normal send 状态恢复
5. Create 输出卡片/状态显示完成/就绪

**弱信号（不要單獨使用）**：
6. 时间已过去足够长
7. UI 看起来空闲但没有明确的完成标记

---

## 4) Get share link (Phase 3) - 自动化获取

收到子代理「完成」返回后，**立即自动执行**：

1. **必须先获取新 snapshot** — 获取最新的 element ref
   - 建议用 `selector="main"` 只取主要对话区域，省 token
2. 点击「分享及匯出」按钮（使用 snapshot 中最新的 ref）
3. 等待分享对话框出现
4. **再次 snapshot** 获取对话框中的最新 ref
   - 可用 `selector=".dialog-content"` 只取对话框内容
5. 点击「公開分享連結」或复制链接
6. 返回给用户

### 精準的 Selector（Deep Research 面板）

```
# 分享按鈕（深層研究面板內）
selector="button[data-test-id='share-button']"

# 或透過 aria-label
selector="button[aria-label='分享報表']"

# 匯出選單按鈕
selector="button[data-test-id='export-menu-button']"
selector="button[aria-label='匯出選單']"
```

⚠️ 每次 click 前都必須先 snapshot！

---

## 5) Create features execution

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

## 6) Return output to user

When done, return:

1. short actionable summary first,
2. key conclusion and market stage (if research report),
3. actionable checklist,
4. source-quality note when references are mixed quality.

Then immediately run post-completion handoff:
- provide one compact next-action menu,
- default suggest **建立語音摘要** if user has no preference,
- execute selected Create/export action directly.

---

## 7) Do not (anti-patterns)

- Do not use `profile="chrome"` — always use `profile="openclaw"` for stability
- Do not assume "plan shown" means "research running"
- Do not judge completion from one weak signal only
- Do not ask the user to manually open tabs, click buttons, or enable any relay
- Do not skip `sessions_spawn` for long-running Deep Research/Create jobs
- Do not dump full long report before giving concise summary
- Do not restart gateway or browser — it's not needed with openclaw profile
- Do not wait for user to ask for share link — automatically get it after completion
- **Do not use old element refs** — always snapshot before every click/type/act operation
- **Do not skip snapshot** — before any interaction, get a fresh snapshot first
