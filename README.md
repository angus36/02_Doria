# 朵莉雅（Doriya）— LINE AI 記帳小精靈

> **Release: `v0.2.0` — public, sanitized n8n workflow template**

使用 **n8n + LINE Messaging API + Google Sheets + Google Gemini + Gmail** 建立的記帳工作流。
此專案採用公開範本格式，保留「02_朵莉雅」工作流名稱與原始節點設計。

## 功能概覽

- LINE 文字記帳：AI 判斷收入／支出並擷取多筆交易。
- LINE 圖片記帳：解析收據項目，逐筆加入支出紀錄。
- 收支查詢：解析日期區間，顯示 LINE Flex Message。
- 收入與支出的更正流程：依原始資料匹配交易，驗證後更新 Google Sheets。
- 每月匯出：彙整上一個月的收支資訊，以 Gmail 寄送檔案。
- 每日提醒：彙整支出類別、金額及冷知識，透過 LINE Flex 推播。

> **目前限制**：操作分類器有 `delete` 意圖，但這份工作流的 CUD 路由未完整連接刪除分支；不要將刪除紀錄視為已實作功能。財務查詢/更新的跨使用者存取隔離尚未完成全面驗證，參見 [SECURITY.md](SECURITY.md)。

## 匯入與設定

1. 從 `workflows/02-doriya.json` 匯入 n8n（Import from File）。匯入後工作流為 **Inactive**，不會自動執行。
2. 重新綁定所有 Credential：**LINE Custom Auth、Google Sheets OAuth2、Google Gemini、Gmail OAuth2**。LINE Custom Auth 的 `Authorization` 應在 n8n Credential 內設定，請勿寫入公開 JSON。
3. 在所有 **17 個 Google Sheets 節點**，依下表重新選擇 Spreadsheet 和 Sheet；此公開範本已移除原始文件 ID 與工作表 GID。
4. 在 `Config` 節點將 `lineTo` 的佔位值改成自己的 LINE User ID；在 `send to me` 節點設定自己的收件信箱。
5. LINE Developers 將 Webhook URL 指向新 n8n 工作流的 Webhook。**正式使用前請驗證 LINE `x-line-signature`**；請先測試各路徑，再自行啟用工作流。

### Google Sheets 結構對照

| Google Sheet 類型 | 工作表名稱 | 用途 |
|---|---|---|
| Finance Spreadsheet | `user` | LINE 使用者對應表 |
| Finance Spreadsheet | `spend_storage` | 支出紀錄主要存放區 |
| Finance Spreadsheet | `earn_storage` | 收入紀錄主要存放區 |
| Finance Spreadsheet | `spend_data` | 支出查詢／日報資料來源 |
| Finance Spreadsheet | `earn_data` | 收入查詢資料來源 |
| Trivia Spreadsheet | `facts` | 冷知識資料來源 |

實際欄位名稱與型別需和 Google Sheets 節點內的 Column Mapping 一致，例如 `userID`、`date`、`time`、`amount`、`category`、`tags`。本套件**不含真實記帳資料或試算表副本**。

### LINE Custom Auth 範例（僅填在 n8n Credential UI，勿提交真實 Token）

```json
{
  "headers": {
    "Authorization": "Bearer YOUR_LINE_CHANNEL_ACCESS_TOKEN",
    "Content-Type": "application/json; charset=UTF-8"
  }
}
```

## 版本管理

使用 [Semantic Versioning](https://semver.org/) 風格的 Tag：`vMAJOR.MINOR.PATCH`。在達到穩定公開版前使用 `0.x.y`；本次為 `v0.2.0`，不代表破壞性變更的 `v2.0.0`。

標準 Repository 名稱：`doriya-finance-bot`；維持固定工作流路徑 `workflows/02-doriya.json`，透過 Git Tag 和 [CHANGELOG.md](CHANGELOG.md) 記錄版本。

### 更新 GitHub（已有 Remote 的情況）

```bash
git add workflows/02-doriya.json README.md CHANGELOG.md SECURITY.md .gitignore
git commit -m "chore(release): prepare v0.2.0"
git tag v0.2.0
git push origin main
git push origin v0.2.0
```

若原本已經有 `v0.2.0` Tag，先使用其他尚未使用的版本號；不要強制覆寫既有公開 Tag。

## 安全說明

原始工作流含使用者資料及特定帳號設定，請**只**提交本套件中的公開版本。不要提交原始 n8n 匯出 JSON、Webhook 執行紀錄、Pin Data 或任何 Credential 匯出檔。

此公開版本是**可匯入的設定範本**，不是開箱即用的生產部署套件。真正啟用前仍需驗證 LINE 簽章與記帳資料存取權限。

## 授權

目前尚未指定開源授權；請依自己的選擇另行新增 `LICENSE`（例如 MIT），以明確授予其他人使用與修改權限。
