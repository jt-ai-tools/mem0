# @mem0/openclaw-mem0

為 [OpenClaw](https://github.com/openclaw/openclaw) 代理提供由 [mem0](https://mem0.ai) 驅動的長期記憶功能。

您的代理通常在對話結束後就會忘記一切。此外掛程式解決了這個問題：它會監視對話、提取重要資訊，並在相關時自動將其帶回上下文。

## 運作原理

<p align="center">
  <img src="../docs/images/openclaw-architecture.png" alt="架構圖" width="800" />
</p>

**自動召回 (Auto-Recall)** — 在代理回應之前，外掛程式會搜尋 mem0 中與當前訊息匹配的記憶，並將其注入上下文中。

**自動捕捉 (Auto-Capture)** — 在代理回應之後，外掛程式會將交流內容發送至 mem0。mem0 會決定哪些資訊值得保留——新事實會被儲存、過時的會被更新、重複的會被合併。

兩者皆在幕後安靜執行。無需額外的提示詞、無需配置，也無需手動呼叫。

### 短期記憶 vs 長期記憶

記憶被組織為兩種範圍：

- **對話 (Session)（短期）** — 自動捕捉會透過 mem0 的 `run_id` / `runId` 參數，將記憶儲存在當前對話範圍內。這些記憶與進行中的對話具備上下文相關性，並會與長期記憶一同被自動召回。

- **使用者 (User)（長期）** — 代理可以透過 `memory_store` 工具（預設 `longTerm: true`）明確儲存長期記憶。這些記憶將為該使用者跨所有對話持久存在。

在**自動召回**期間，外掛程式會搜尋這兩種範圍並分別呈現——優先顯示長期記憶，其次是對話記憶——確保代理擁有完整的上下文。

代理工具（`memory_search`、`memory_list`）接受 `scope` 參數（`"session"`、`"long-term"` 或 `"all"`）來控制要查詢哪些記憶。`memory_store` 工具則接受 `longTerm` 布林值（預設：`true`）來選擇儲存位置。

所有新參數皆為選填且向下相容——現有的配置無需修改即可運作。

## 設定

```bash
openclaw plugins install @mem0/openclaw-mem0
```

### 平台模式 (mem0 Cloud)

從 [app.mem0.ai](https://app.mem0.ai) 獲取 API 金鑰，然後新增至您的 `openclaw.json`：

```json5
// plugins.entries
"openclaw-mem0": {
  "enabled": true,
  "config": {
    "apiKey": "${MEM0_API_KEY}",
    "userId": "您的-使用者-id"
  }
}
```

### 開源模式 (自行託管)

不需要 mem0 金鑰。需要 `OPENAI_API_KEY` 以使用預設的嵌入向量/LLM。

```json5
"openclaw-mem0": {
  "enabled": true,
  "config": {
    "mode": "open-source",
    "userId": "您的-使用者-id"
  }
}
```

開箱即用，具備合理的預設值。若要自定義嵌入器、向量儲存或 LLM：

```json5
"config": {
  "mode": "open-source",
  "userId": "您的-使用者-id",
  "oss": {
    "embedder": { "provider": "openai", "config": { "model": "text-embedding-3-small" } },
    "vectorStore": { "provider": "qdrant", "config": { "host": "localhost", "port": 6333 } },
    "llm": { "provider": "openai", "config": { "model": "gpt-4o" } }
  }
}
```

所有 `oss` 欄位皆為選填。提供商資訊請參閱 [mem0 開源版文件](https://docs.mem0.ai/open-source/node-quickstart)。

## 代理工具

代理在對話期間可以使用五種工具：

| 工具 | 描述 |
|------|-------------|
| `memory_search` | 使用自然語言搜尋記憶 |
| `memory_list` | 列出為使用者儲存的所有記憶 |
| `memory_store` | 明確儲存一個事實 |
| `memory_get` | 根據 ID 檢索記憶 |
| `memory_forget` | 根據 ID 或查詢進行刪除 |

## 命令列介面 (CLI)

```bash
# 搜尋所有記憶（長期 + 對話）
openclaw mem0 search "使用者懂得哪些語言"

# 僅搜尋長期記憶
openclaw mem0 search "使用者懂得哪些語言" --scope long-term

# 僅搜尋對話/短期記憶
openclaw mem0 search "使用者懂得哪些語言" --scope session

# 統計數據
openclaw mem0 stats
```

## 選項設定

### 一般設定

| 鍵 (Key) | 類型 | 預設值 | 說明 |
|-----|------|---------|---|
| `mode` | `"platform"` \| `"open-source"` | `"platform"` | 使用哪種後端 |
| `userId` | `string` | `"default"` | 按使用者區分記憶範圍 |
| `autoRecall` | `boolean` | `true` | 在每個對話回合前注入記憶 |
| `autoCapture` | `boolean` | `true` | 在每個對話回合後儲存事實 |
| `topK` | `number` | `5` | 每次召回的最大記憶數量 |
| `searchThreshold` | `number` | `0.3` | 最小相似度 (0–1) |

### 平台模式

| 鍵 (Key) | 類型 | 預設值 | 說明 |
|-----|------|---------|---|
| `apiKey` | `string` | — | **必填。** mem0 API 金鑰（支援 `${MEM0_API_KEY}`） |
| `orgId` | `string` | — | 組織 ID |
| `projectId` | `string` | — | 專案 ID |
| `enableGraph` | `boolean` | `false` | 用於關係的實體圖形 |
| `customInstructions` | `string` | *(內建)* | 提取規則 — 儲存內容與格式化方式 |
| `customCategories` | `object` | *(12 個預設值)* | 類別名稱 → 標記用描述對照表 |

### 開源模式

無需額外配置即可運作。`oss` 區塊讓您可以更換任何組件：

| 鍵 (Key) | 類型 | 預設值 | 說明 |
|-----|------|---------|---|
| `customPrompt` | `string` | *(內建)* | 用於記憶處理的提取提示詞 |
| `oss.embedder.provider` | `string` | `"openai"` | 嵌入向量提供商 (`"openai"`, `"ollama"` 等) |
| `oss.embedder.config` | `object` | — | 提供商配置：`apiKey`, `model`, `baseURL` |
| `oss.vectorStore.provider` | `string` | `"memory"` | 向量儲存 (`"memory"`, `"qdrant"`, `"chroma"` 等) |
| `oss.vectorStore.config` | `object` | — | 提供商配置：`host`, `port`, `collectionName`, `dimension` |
| `oss.llm.provider` | `string` | `"openai"` | LLM 提供商 (`"openai"`, `"anthropic"`, `"ollama"` 等) |
| `oss.llm.config` | `object` | — | 提供商配置：`apiKey`, `model`, `baseURL`, `temperature` |
| `oss.historyDbPath` | `string` | — | 用於記憶編輯歷史的 SQLite 路徑 |

`oss` 內的所有內容皆為選填 — 預設使用 OpenAI 嵌入向量 (`text-embedding-3-small`)、記憶體內向量儲存及 OpenAI LLM。僅需覆寫您需要的部分。

## 授權協議

Apache 2.0
