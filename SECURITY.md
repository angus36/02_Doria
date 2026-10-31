# Security / 安全檢查

## 已完成的靜態清理（v0.2.0）

| 類別 | 處理 |
|---|---|
| LINE Channel Access Token | 原始最新 JSON 未找到節點中的明文 Bearer Token；所有 LINE 節點透過 Custom Auth 連接（公開範本未綁定 Credential） |
| LINE Webhook Pin Data | 已全部移除（其中原本包含使用者 ID、簽章、主機名稱及訊息內容） |
| Google Sheets Document IDs | 清除 17 個節點中的文件 ID 與已快取 URL（涉及兩份 Spreadsheet） |
| Google Sheets Sheet IDs | 清除 17 個節點中的 GID；需重新選取 Sheet |
| LINE User ID | `Config.lineTo` 更換為範例佔位值 |
| Gmail | 寄送至 `your-email@example.com`，須自行設定 |
| n8n Credential references | 全數移除 ID 與名稱；匯入後自行綁定 |
| n8n instance/workflow identifiers | 清除環境專屬 ID、Version ID、Instance ID，以及 Webhook ID |
| 專屬 Prompt 範例 | 泛化與個人背景相關的機構名稱 |

## 部署前必修的安全事項

1. **LINE Webhook 驗證**：工作流中未找到對原始請求與 `x-line-signature` 進行 HMAC-SHA256 驗證的保護節點。正式公開 Webhook 前，請在適當入口層驗證簽章，避免偽造事件。
2. **資料隔離**：`read_spend`、`read_earn` 及相關查詢分支只看到日期條件，沒有確認依 LINE `userID` 全面做資料隔離。`Pack Earn Rows` 也未檢查候選紀錄的擁有者；多人使用時可能洩漏或修改其他使用者的財務資料。
3. **最小權限**：Google Sheets/Gmail 的 OAuth 帳號只授權專案必需的資料與操作權限。
4. **憑證管理**：所有 Access Token、API Key、OAuth Token 保存在 n8n Credentials 中。若舊的明文 LINE Token 曾上傳到 GitHub，應立即撤銷並重發，刪除檔案不等於撤銷憑證。
5. **測試資料**：未來重新匯出 n8n JSON 時，要再次刪除 `pinData`、檢查寫死的使用者 ID、信箱、試算表連結和新加入的 HTTP Header。

## 審查邊界

本次為**靜態敏感資訊掃描和去識別化**；無法保證所有功能可直接執行，亦不等於完成 Webhook 及多使用者授權的安全測試。保留原始 Code 節點與資料處理邏輯，未將所有業務問題視為本次修改範圍。
