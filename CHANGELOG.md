# Changelog

版本命名遵循 Semantic Versioning 格式（`vMAJOR.MINOR.PATCH`）。

## [0.2.0] - 2026-11-01

### Changed
- 以使用者提供的最新 `02_朵莉雅` 工作流重新建立公開範本，共 84 個節點。
- LINE HTTP Request 節點已使用 n8n Custom Auth，憑證需自行設定。
- 清除所有 Google Sheets 文件識別碼及工作表 GID，保留工作表名稱供匯入後重新選取。
- 將固定 LINE 推播收件者、Gmail 收件者替換為安全佔位值。
- 將提示詞中的特定任職機構改成泛用名稱。

### Security
- 刪除固定的 LINE Webhook 測試事件（Pin Data）。
- 清除 Credential 的 ID／名稱參照、工作流及執行個體識別資訊。
- 將工作流預設設為 Inactive，停用 `availableInMCP`。

### Note
- 本次僅清理公開敏感資訊，沒有變更交易計算、資料匹配、AI 提示詞邏輯或節點連線。
- 無法從靜態 JSON 驗證 n8n 執行時行為或所有部署安全設定。
