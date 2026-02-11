# mem0 - 為您的 AI 應用程式打造的記憶層

mem0 是一個為 LLM 應用程式打造的自適應記憶層，能實現個人化的 AI 體驗，同時節省成本並提升使用者滿意度。我們提供雲端與開源兩種方案，以滿足不同的需求。

請參閱完整的 [開源版文件 (OSS Docs)](https://docs.mem0.ai/open-source/node-quickstart)。
請參閱完整的 [平台 API 參考 (Platform API Reference)](https://docs.mem0.ai/api-reference)。

## 1. 安裝

針對開源版本，您可以使用 npm 安裝 mem0 套件：

```bash
npm i mem0ai
```

## 2. API 金鑰設定

針對雲端產品，請登入 [mem0 平台](https://app.mem0.ai/dashboard/api-keys) 以取得您的 API 金鑰。

## 3. 客戶端功能

### 雲端方案 (Cloud Offering)

雲端版本提供一套完整的功能，包括：

- **記憶操作**：對記憶執行 CRUD 操作。
- **搜尋能力**：使用進階過濾器搜尋相關記憶。
- **記憶歷史記錄**：追蹤記憶隨時間的變更。
- **錯誤處理**：針對 API 相關問題的強健錯誤處理機制。
- **Async/Await 支援**：所有方法皆回傳 Promise，方便整合。

### 開源方案 (Open-Source Offering)

開源版本包含以下核心功能：

- **記憶管理**：新增、更新、刪除及檢索記憶。
- **向量儲存整合**：支援多種向量儲存提供商，以實現高效的記憶檢索。
- **LLM 支援**：與多個 LLM 提供商整合以生成回應。
- **可自定義配置**：輕鬆配置記憶設定與提供商。
- **SQLite 儲存**：使用 SQLite 進行記憶歷史管理。

## 4. 記憶操作

mem0 提供一個簡單且可自定義的介面來執行記憶操作。您可以建立長期與短期記憶、搜尋相關記憶並管理記憶歷史。

## 5. 錯誤處理

MemoryClient 會針對任何 API 相關問題拋出錯誤。您可以有效地捕捉並處理這些錯誤。

## 6. 搭配 async/await 使用

MemoryClient 的所有方法皆回傳 Promise，可與 async/await 語法無縫整合。

## 7. 測試客戶端

若要在 Node.js 環境中測試 MemoryClient，您可以建立一個簡單的腳本來驗證記憶操作的功能。

## 獲取協助

如果您有任何疑問或需要協助，請透過以下方式聯絡我們：

- 電子郵件：founders@mem0.ai
- [加入我們的 Discord 社群](https://mem0.ai/discord)
- GitHub Issues：[回報錯誤或請求新功能](https://github.com/mem0ai/mem0/issues)
