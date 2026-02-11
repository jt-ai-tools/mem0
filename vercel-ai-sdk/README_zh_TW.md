# mem0 AI SDK 提供商

**mem0 AI SDK 提供商**是由 [mem0](https://mem0.ai/) 開發並由社群維護的函式庫，用於與 Vercel AI SDK 整合。透過引入持久化記憶功能，此函式庫為您的應用程式帶來增強型 AI 互動能力。藉由 mem0，語言模型對話將具備記憶力，進而能根據過往互動提供更具上下文感知的個人化回應。

在 [GitHub](https://github.com/mem0ai) 上探索更多 **mem0** 的資訊。
造訪 [mem0 說明文件](https://docs.mem0.ai/overview) 以獲取對記憶管理的深層控制與靈活性。

有關使用 Vercel AI SDK 的詳細資訊，請參閱 Vercel 的 [API 參考](https://sdk.vercel.ai/docs/reference) 與 [說明文件](https://sdk.vercel.ai/docs)。

## 功能

- 🧠 AI 對話的持久化記憶儲存
- 🔄 與 Vercel AI SDK 無縫整合
- 🚀 支援多種 LLM 提供商
- 📝 支援豐富的訊息格式
- ⚡ 支援串流 (Streaming) 能力
- 🔍 上下文感知回應

## 安裝

```bash
npm install @mem0/vercel-ai-provider
```

## 開始之前

### 設定 mem0

1. 從 mem0 控制面板獲取您的 [mem0 API 金鑰](https://app.mem0.ai/dashboard/api-keys)。

2. 初始化 mem0 客戶端：

```typescript
import { createMem0 } from "@mem0/vercel-ai-provider";

const mem0 = createMem0({
  provider: "openai",
  mem0ApiKey: "m0-xxx",
  apiKey: "openai-api-key",
  config: {
    compatibility: "strict",
    // 可以在此處加入額外的模型特定配置選項。
  },
});
```

### 備註
預設使用 `openai` 提供商，因此指定它是選填的：
```typescript
const mem0 = createMem0();
```
為了提高安全性，建議將 `MEM0_API_KEY` 和 `OPENAI_API_KEY` 設定為環境變數。

3. 新增記憶以增強上下文：

```typescript
import { LanguageModelV1Prompt } from "ai";
import { addMemories } from "@mem0/vercel-ai-provider";

const messages: LanguageModelV1Prompt = [
  {
    role: "user",
    content: [
      { type: "text", text: "我喜歡紅色的車。" },
      { type: "text", text: "我喜歡 Toyota 的車。" },
      { type: "text", text: "我偏好 SUV。" },
    ],
  },
];

await addMemories(messages, { user_id: "borat" });
```

這些記憶現在已儲存於您的個人檔案中。您可以在 [mem0 控制面板](https://app.mem0.ai/dashboard/users) 上查看並管理它們。

### 注意事項：

針對獨立功能（如 `addMemories` 與 `retrieveMemories`），您必須設定 `MEM0_API_KEY` 環境變數，或直接在函式呼叫中傳遞。

範例：

```typescript
await addMemories(messages, { user_id: "borat", mem0ApiKey: "m0-xxx", org_id: "org_xx", project_id: "proj_xx" });
await retrieveMemories(prompt, { user_id: "borat", mem0ApiKey: "m0-xxx", org_id: "org_xx", project_id: "proj_xx" });
await getMemories(prompt, { user_id: "borat", mem0ApiKey: "m0-xxx", org_id: "org_xx", project_id: "proj_xx" });
```

### 注意事項：

`retrieveMemories` 會使用個人檔案中的相關記憶來增強提示詞，而 `getMemories` 則以陣列格式回傳記憶，可用於進一步處理。

## 用法範例

### 1. 具備記憶上下文的基礎文字生成

```typescript
import { generateText } from "ai";
import { createMem0 } from "@mem0/vercel-ai-provider";

const mem0 = createMem0();

const { text } = await generateText({
  model: mem0("gpt-4-turbo", {
    user_id: "borat",
  }),
  prompt: "推薦我一部值得買的好車！",
});
```

### 2. 結合 OpenAI 提供商與記憶工具

```typescript
import { generateText } from "ai";
import { openai } from "@ai-sdk/openai";
import { retrieveMemories } from "@mem0/vercel-ai-provider";

const prompt = "推薦我一部值得買的好車。";
const memories = await retrieveMemories(prompt, { user_id: "borat" });

const { text } = await generateText({
  model: openai("gpt-4-turbo"),
  prompt: prompt,
  system: memories,
});
```

### 3. 具備記憶的結構化訊息格式

```typescript
import { generateText } from "ai";
import { createMem0 } from "@mem0/vercel-ai-provider";

const mem0 = createMem0();

const { text } = await generateText({
  model: mem0("gpt-4-turbo", {
    user_id: "borat",
  }),
  messages: [
    {
      role: "user",
      content: [
        { type: "text", text: "推薦我一部值得買的好車。" },
        { type: "text", text: "為什麼它對我來說比其他車更好？" },
        { type: "text", text: "請提供各個價格範圍的選項。" },
      ],
    },
  ],
});
```

### 4. 與 OpenAI 的進階記憶整合

```typescript
import { generateText, LanguageModelV1Prompt } from "ai";
import { openai } from "@ai-sdk/openai";
import { retrieveMemories } from "@mem0/vercel-ai-provider";

// 使用 system 參數作為記憶上下文的新格式
const messages: LanguageModelV1Prompt = [
  {
    role: "user",
    content: [
      { type: "text", text: "推薦我一部值得買的好車。" },
      { type: "text", text: "為什麼它對我來說比其他車更好？" },
      { type: "text", text: "請提供各個價格範圍的選項。" },
    ],
  },
];

const memories = await retrieveMemories(messages, { user_id: "borat" });

const { text } = await generateText({
  model: openai("gpt-4-turbo"),
  messages: messages,
  system: memories,
});
```

### 5. 具備記憶上下文的串流回應

```typescript
import { streamText } from "ai";
import { createMem0 } from "@mem0/vercel-ai-provider";

const mem0 = createMem0();

const { textStream } = await streamText({
  model: mem0("gpt-4-turbo", {
    user_id: "borat",
  }),
  prompt:
    "推薦我一部值得買的好車！為什麼它對我來說比其他車更好？請提供各個價格範圍的選項。",
});

for await (const textPart of textStream) {
  process.stdout.write(textPart);
}
```

## 核心函式

- `createMem0()`：初始化一個具備可選配置的 mem0 提供商實例。
- `retrieveMemories()`：使用相關記憶增強提示詞。
- `addMemories()`：將記憶新增至您的個人檔案中。
- `getMemories()`：以陣列格式從個人檔案中獲取記憶。

## 配置選項

```typescript
const mem0 = createMem0({
  config: {
    ...
    // 可以在此處加入額外的模型特定配置選項。
  },
});
```

## 最佳實踐

1. **使用者識別**：務必提供唯一的 `user_id` 識別碼，以確保一致的記憶檢索。
2. **上下文管理**：使用適當的上下文視窗大小來平衡效能與記憶。
3. **錯誤處理**：為記憶操作實作適當的錯誤處理。
4. **記憶清理**：定期清理未使用的記憶上下文以優化效能。

我們亦支援 `agent_id`、`app_id` 與 `run_id`。請參閱 [說明文件](https://docs.mem0.ai/api-reference/memory/add-memories)。

## 備註

- 需要為底層提供商（如 OpenAI）配置正確的 API 金鑰。
- 記憶功能依賴於透過 `user_id` 進行正確的使用者識別。
- 同時支援串流與非串流回應。
- 相容於所有 Vercel AI SDK 的功能與模式。
