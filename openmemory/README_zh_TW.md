# OpenMemory

OpenMemory 是專為 LLM 打造的個人記憶層——具備私密性、便攜性且完全開源。您的記憶儲存在本地，讓您對數據擁有完整的控制權。在構建具備個人化記憶的 AI 應用程式的同時，確保數據安全性。

![OpenMemory](https://github.com/user-attachments/assets/3c701757-ad82-4afa-bfbe-e049c2b4320b)

## 輕鬆設定

### 前置需求
- Docker
- OpenAI API 金鑰

您可以透過執行以下指令快速啟動 OpenMemory：

```bash
curl -sL https://raw.githubusercontent.com/mem0ai/mem0/main/openmemory/run.sh | bash
```

您應將 `OPENAI_API_KEY` 設定為全域環境變數：

```bash
export OPENAI_API_KEY=您的-API-金鑰
```

您也可以將 `OPENAI_API_KEY` 作為參數傳遞給腳本：

```bash
curl -sL https://raw.githubusercontent.com/mem0ai/mem0/main/openmemory/run.sh | OPENAI_API_KEY=您的-API-金鑰 bash
```

## 前置需求

- Docker 和 Docker Compose
- Python 3.9+ (用於後端開發)
- Node.js (用於前端開發)
- OpenAI API 金鑰（用於 LLM 互動，執行 `cp api/.env.example api/.env` 並將 **OPENAI_API_KEY** 更改為您的金鑰）

## 快速上手

### 1. 設定環境變數

在執行專案之前，您需要為 API 和 UI 配置環境變數。

您可以透過以下任一方式進行設定：

- **手動設定**：
  在以下每個目錄中建立 `.env` 檔案：
  - `/api/.env`
  - `/ui/.env`

- **使用 `.env.example` 檔案**：
  複製並重新命名範例檔案：

  ```bash
  cp api/.env.example api/.env
  cp ui/.env.example ui/.env
  ```

 - **使用 Makefile**（如果支援）：
    執行：
  
   ```bash
   make env
   ```
- #### `/api/.env` 範例

```env
OPENAI_API_KEY=sk-xxx
USER=<user-id> # 您想要關聯記憶的使用者 ID
```
- #### `/ui/.env` 範例

```env
NEXT_PUBLIC_API_URL=http://localhost:8765
NEXT_PUBLIC_USER_ID=<user-id> # 與 api 環境變數中的使用者 ID 相同
```

### 2. 構建並執行專案
您可以使用以下兩個指令來執行專案：
```bash
make build # 構建 mcp 伺服器與 ui
make up    # 執行 openmemory mcp 伺服器與 ui
```

執行這些指令後，您將擁有：
- 執行於 http://localhost:8765 的 OpenMemory MCP 伺服器（API 文件可於 http://localhost:8765/docs 存取）
- 執行於 http://localhost:3000 的 OpenMemory UI

#### UI 無法在 `localhost:3000` 運作？

如果 UI 未在 [http://localhost:3000](http://localhost:3000) 正常啟動，請嘗試手動執行：

```bash
cd ui
pnpm install
pnpm dev
```

### MCP 客戶端設定

使用以下單一步驟指令將 OpenMemory 本地 MCP 配置到客戶端。通用指令格式如下：

```bash
npx @openmemory/install local http://localhost:8765/mcp/<client-name>/sse/<user-id> --client <client-name>
```

將 `<client-name>` 替換為所需的客戶端名稱，並將 `<user-id>` 替換為您在環境變數中指定的值。


## 專案結構

- `api/` - 後端 API + MCP 伺服器
- `ui/` - 前端 React 應用程式

## 貢獻

我們是一群熱愛 AI 未來與開源軟體的開發者。憑藉在兩個領域多年的經驗，我們相信社群驅動開發的力量，並很高興能構建使 AI 更易於存取且更具個人化的工具。

我們歡迎各種形式的貢獻：
- 錯誤回報與功能請求
- 文件改進
- 程式碼貢獻
- 測試與回饋
- 社群支援

如何貢獻：

1. Fork 儲存庫。
2. 建立您的功能分支（`git checkout -b openmemory/feature/amazing-feature`）。
3. 提交您的變更（`git commit -m 'Add some amazing feature'`）。
4. 推送到分支（`git push origin openmemory/feature/amazing-feature`）。
5. 發起 Pull Request。

加入我們，共同構建 AI 記憶管理的未來！您的貢獻將幫助 OpenMemory 變得更好。
