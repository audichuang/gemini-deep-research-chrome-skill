# Post-Completion Actions (After Research is Done)

When Deep Research reaches completion, do not stop at summary only.

## Mandatory handoff step

After reporting completion, always offer next actions in one compact menu:

1. 建立語音摘要
2. 建立資訊圖表
3. 建立測驗
4. 建立學習卡
5. 建立網頁
6. 分享及匯出

If user does not choose, suggest the most useful default:
- first default: **建立語音摘要** (quick consumption)

## Operator behavior

1. Detect completion using the Completion Signals in SKILL.md §3.
2. Return concise report summary.
3. Immediately provide the next-action menu above.
4. If user confirms an action, execute it directly.

## Create-action execution shortcuts

### 建立語音摘要
1. Click **建立** → **建立語音摘要**.
2. Fill prompt/description (language/style if requested).
3. Submit and monitor until done.
4. Open **分享及匯出** (or equivalent download entry) and download the generated audio file.
5. Send the downloaded audio file back to the user in chat (do not only say it is ready).
6. Then return a short note with what was sent and optional follow-up actions.

### 建立資訊圖表 / 測驗 / 學習卡 / 網頁
1. Click **建立**.
2. Select target output.
3. Provide focused prompt.
4. Monitor completion and return result + next suggested step.

## Do not

- Do not end interaction right after saying "已完成".
- Do not wait for user to rediscover available Create options manually.
- Do not force all options; provide one compact menu and proceed by user choice.
