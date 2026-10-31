# 朵莉雅｜n8n × LINE 記帳小精靈

使用 n8n 工作流串接 LINE Messaging API、Google Sheets、Gemini 與 Gmail，提供文字／圖片記帳、收入與支出分類、收支查詢、紀錄修改、每日提醒與月報寄送等功能。

> `workflow.json` 是**已去識別化的展示及匯入範本**，不包含可直接使用的憑證、個人收件者或 Google Sheets 文件 ID。公開版本預設停用，匯入後必須自行設定。

## 匯入與設定

1. 在 n8n 建立新 Workflow，選擇 **Import from File**，匯入 `workflow.json`。
2. 建立 LINE Messaging API 的 **Header Auth** Credential：Header Name 設 `Authorization`，Value 設 `Bearer <你自己的 Channel Access Token>`。將它指定到所有 LINE HTTP Request 節點。
3. 建立自己的 **Google Sheets OAuth2**、**Gemini API** 與 **Gmail OAuth2** Credentials，分別重新選擇工作流中相關節點的 Credential。
4. 將 17 個 Google Sheets 節點的 `REPLACE_WITH_YOUR_SPREADSHEET_ID` 替換成自己的文件 ID，並在每個節點重新選擇對應的工作表（`user`、`spend_data`、`earn_data`、`spend_storage`、`earn_storage`，以及冷知識 `facts`）。建立相應欄位結構；本範本不包含實際資料庫內容。
5. 在 `Config` 節點將 `REPLACE_WITH_YOUR_LINE_USER_ID` 改成自己的 LINE 接收者 User ID，並於 Gmail `send to me` 節點替換 `your-email@example.com`。接著測試各分支，再開啟排程／Webhook。

## 安全須知

- **嚴禁**將 LINE Channel Access Token、Gemini API Key、Google OAuth Token、Channel Secret 或其他憑證寫進公開 JSON。
- 不要公開帶有 **Pin Data** 的 n8n 匯出檔。Pinned Webhook Payload 可能包含聊天文字、User ID、Reply Token、來源 IP 等資料。
- 請在正式接收 LINE Webhook 時**驗證 `x-line-signature`**（使用接收時的原始 body + Channel Secret 計算 HMAC-SHA256，務必在處理事件前驗證）。若 n8n Webhook 配置拿不到原始請求內容，請使用可驗證原文的前置服務。
- 如預計多人使用，所有讀取、修改、刪除流程應以 LINE `source.userId` 限制可操作的紀錄。原始設計部分支線尚需做多使用者隔離安全審核。
- 避免在日誌或公開 Issue 中貼出實際收支紀錄、手機號碼、收件信箱或 API 回應內容。
- 如曾將含憑證的版本推上公開 GitHub，應**撤銷／重新簽發權杖**，並檢查 Git 歷史；只修改最新 commit 無法消除歷史紀錄。

## 開源使用說明

`workflow.json` 保留原始流程結構，用於展示與研究；憑證、表格、LINE Bot、Webhook 等外部資源均須自行建立。正式公開前請補充專案採用的授權條款（如 MIT）。
