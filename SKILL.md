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
3. **檢查是否已有 Gemini 分頁** (`gemini.google.com`)。
   - **檢查內容匹配**：如果已有現有分頁，**必須檢查該分頁的研究主題是否與用戶請求匹配**
     - ✅ **匹配**：例如用戶問「東京機票」，現有分頁標題也是「東京機票」→ 繼續使用
     - ❌ **不匹配**：例如用戶問「北海道」，現有分頁是「福岡」或「東京」→ **必須開新分頁**，導航至 `https://gemini.google.com/app` 開始新研究
   - **如果沒有**：開啟新分頁，導航至 `https://gemini.google.com`
4. Do NOT ask the user to open tabs or click any buttons.

**⚠️ 每次操作前必須先 snapshot！** Gemini 頁面的 element ref 每次都會變，絕對不要使用超過 30 秒前的 element ref。

**🔴 強制要求：必須使用穩定 selector 省 token！**
```javascript
// ✅ 正確：優先使用穩定 selector（避免抓整頁）
browser snapshot profile=openclaw selector="main"
browser snapshot profile=openclaw selector="button[data-test-id='share-button']"
browser snapshot profile=openclaw selector="[role='dialog']"

// ✅ 退而求其次：當 data-test-id 不存在時
browser snapshot profile=openclaw selector="button[aria-label='分享報表']"
browser snapshot profile=openclaw selector="button[aria-label='匯出選單']"

// ❌ 錯誤：不要取整頁，會浪費大量 token 且 ref 不穩
// browser snapshot profile=openclaw
```

**⚠️ 重要：如果沒有使用 selector 或 selector 錯誤，snapshot 的內容會不同，element ref 也會改變！**
- 沒有 selector → 取整頁 → element ref 是完整的（如 e1, e20, e135）
- 有 selector → 只取部分 → element ref 是局部的（如 e1, e5, e10）
- **這會導致你用錯誤的 ref 去 click，出現 "Element not found"！**

**解決方法：**
1. 始終使用相同的 selector 模式（建議都用 `selector="main"`）
2. 如果出現 "Element not found"，立即重新 snapshot 取得正確的 ref
3. 不要混用「有 selector」和「沒有 selector」的 snapshot

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
   **必须使用 selector="main" 只取主要对话区，不要取整页！**
   错误：browser snapshot profile=openclaw
   正确：browser snapshot profile=openclaw selector=main
3) 使用下方 Completion Signals 判断是否完成

⚠️ 重要：
- 每次 click 或 act 操作前，必須先執行 browser snapshot 取得最新的 element ref！
- **每次 snapshot 都必須使用 selector="main"，否則會消耗大量 token！**
- 千萬不要使用舊的 ref，否則會出現 'Element not found' 錯誤。

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

**Fallback 提醒**：如果子代理 spawn 失败（如 forbidden 错误），主代理必须立即接管监控任务，自己执行 browser snapshot 检查进度，不能放弃！研究完成后立即获取分享链接。

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

1. **必须先获取新 snapshot** — 必须使用 selector
   ```javascript
   // 正确：使用 selector 只取主要区域
   browser snapshot profile=openclaw selector="main"
   
   // 错误：不要这样，会取整页浪费 token
   // browser snapshot profile=openclaw
   ```
2. 点击「分享及匯出」按钮（使用 snapshot 中最新的 ref）
3. 等待分享对话框出现
4. **再次 snapshot** 必須使用穩定 selector（優先抓 role）
   ```javascript
   // 優先：穩定抓分享對話框
   browser snapshot profile=openclaw selector="[role='dialog']"

   // 備援：若 role 無法抓到再用容器 class
   // browser snapshot profile=openclaw selector=".dialog-content"
   ```
5. 優先從對話框直接讀取可分享連結（如 `gemini.google.com/share/...`）；若沒有顯示，再點「公開分享連結」
6. 返回给用户

### 精準且穩定的 Selector（Deep Research 面板）

```
# 1) 報表分享入口（優先）
selector="button[data-test-id='share-button']"

# 2) 對話框（優先）
selector="[role='dialog']"

# 3) 分享連結（直接抓結果）
selector="a[href*='gemini.google.com/share/']"

# 4) 備援（當 data-test-id 不存在）
selector="button[aria-label='分享報表']"
selector="button[aria-label='匯出選單']"
```

⚠️ **必須使用 selector！** 每次 snapshot 都要加 `selector="main"`，否則會消耗大量 token！

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

## 7) 强制要求与 Fallback 策略

### 🔴 强制要求（必须遵守）

**1. 必须使用 profile="openclaw"**
- 绝对不要使用 profile="chrome"

**2. 必须使用 selector 节省 token**
- ✅ 正确：`browser snapshot profile=openclaw selector="main"`
- ❌ 错误：`browser snapshot profile=openclaw`（会消耗大量 token！）
- 如果没有使用 selector 导致 "Element not found" 或 token 超限，必须重试并加上 selector

**3. 必须每次操作前都 snapshot**
- 每次 click、type、act 之前，必须先执行 browser snapshot
- 绝对不能使用超过 30 秒前的 element ref

### ⚠️ Do not（禁止事项）

- Do not assume "plan shown" means "research running"
- Do not judge completion from one weak signal only
- Do not ask the user to manually open tabs, click buttons, or enable any relay
- Do not skip `sessions_spawn` for long-running Deep Research/Create jobs
- Do not dump full long report before giving concise summary
- Do not restart gateway or browser — it's not needed with openclaw profile
- Do not wait for user to ask for share link — automatically get it after completion

### 🔄 Fallback 策略（当出现问题时）

**如果 sessions_spawn 失败（如 forbidden 错误）：**
1. 不要放弃！主代理必须立即接管监控任务
2. 使用 exec sleep 30/60 等待，然后自己执行 browser snapshot 检查进度
3. 持续监控直到研究完成
4. 完成后立即获取分享链接

**如果 browser 操作失败：**
1. 先檢查 selector 是否穩定（優先 `main`、`button[data-test-id='share-button']`、`[role='dialog']`）
2. 不要盲目整頁 snapshot；只對目標區塊重新 snapshot 取得最新 ref
3. 若分享菜單打不開，改走：`share-button` → `[role='dialog']` → `a[href*='gemini.google.com/share/']`
4. 如果还是失败，检查 browser status 和 tabs 状态
5. 不要 restart gateway（openclaw profile 不需要）

**如果 token 超限（429 错误）：**
1. 减少 snapshot 频率（从 30 秒延长到 60-90 秒）
2. 确保每次都使用 selector="main" 减少 token 消耗
3. 等待几分钟后再继续
