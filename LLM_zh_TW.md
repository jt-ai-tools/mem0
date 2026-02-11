# mem0 - 為個人化 AI 打造的記憶層

## 總覽

mem0 ("mem-zero") 是一個智慧記憶層，透過持久且個人化的記憶能力增強 AI 助手和代理 (Agents)。它能讓 AI 系統記住使用者偏好、適應個人需求並隨時間持續學習，非常適合用於客戶支援聊天機器人、AI 助手和自主系統。

**主要優勢：**
- 在 LOCOMO 基準測試中，準確度比 OpenAI Memory 高出 +26%
- 回應速度比全上下文 (Full-context) 方法快 91%
- 提示詞 (Token) 使用量比全上下文方法低 90%

## 安裝

```bash
# Python
pip install mem0ai

# TypeScript/JavaScript
npm install mem0ai
```

## 快速上手

### Python - 自行託管 (Self-Hosted)
```python
from mem0 import Memory

# 初始化記憶
memory = Memory()

# 新增記憶
memory.add([
    {"role": "user", "content": "我喜歡披薩，討厭青花菜"},
    {"role": "assistant", "content": "我會記住您的食物偏好！"}
], user_id="user123")

# 搜尋記憶
results = memory.search("食物偏好", user_id="user123")
print(results)

# 獲取所有記憶
all_memories = memory.get_all(user_id="user123")
```

### Python - 託管平台 (Hosted Platform)
```python
from mem0 import MemoryClient

# 初始化客戶端
client = MemoryClient(api_key="your-api-key")

# 新增記憶
client.add([
    {"role": "user", "content": "我的名字是 John，我是一名開發者"}
], user_id="john")

# 搜尋記憶
results = client.search("您對我有什麼了解？", user_id="john")
```

### TypeScript - 客戶端 SDK
```typescript
import { MemoryClient } from 'mem0ai';

const client = new MemoryClient({ apiKey: 'your-api-key' });

// 新增記憶
const memories = await client.add([
  { role: 'user', content: '我的名字是 John' }
], { user_id: 'john' });

// 搜尋記憶
const results = await client.search('我的名字是什麼？', { user_id: 'john' });
```

### TypeScript - 開源版 (OSS) SDK
```typescript
import { Memory } from 'mem0ai/oss';

const memory = new Memory({
  embedder: { provider: 'openai', config: { apiKey: 'key' } },
  vectorStore: { provider: 'memory', config: { dimension: 1536 } },
  llm: { provider: 'openai', config: { apiKey: 'key' } }
});

const result = await memory.add('我的名字是 John', { userId: 'john' });
```

## 核心 API 參考

### Memory 類別 (自行託管)

**匯入：** `from mem0 import Memory, AsyncMemory`

#### 初始化
```python
from mem0 import Memory
from mem0.configs.base import MemoryConfig

# 基本初始化
memory = Memory()

# 使用自定義配置
config = MemoryConfig(
    vector_store={"provider": "qdrant", "config": {"host": "localhost"}},
    llm={"provider": "openai", "config": {"model": "gpt-4.1-nano-2025-04-14"}},
    embedder={"provider": "openai", "config": {"model": "text-embedding-3-small"}}
)
memory = Memory(config)
```

#### 核心方法

**add(messages, *, user_id=None, agent_id=None, run_id=None, metadata=None, infer=True, memory_type=None, prompt=None)**
- **用途**：從訊息中建立新記憶。
- **參數**：
  - `messages`：字串、字典或訊息字典列表。
  - `user_id/agent_id/run_id`：對話識別碼（至少需要一個）。
  - `metadata`：要存儲的額外元數據。
  - `infer`：是否使用 LLM 進行事實提取（預設：True）。
  - `memory_type`：若為程序性記憶，請設為 "procedural_memory"。
  - `prompt`：建立記憶時使用的自定義提示詞。
- **回傳**：包含 "results" 鍵的字典，內含記憶操作結果。

**search(query, *, user_id=None, agent_id=None, run_id=None, limit=100, filters=None, threshold=None)**
- **用途**：進行語義化記憶搜尋。
- **參數**：
  - `query`：搜尋查詢字串。
  - `user_id/agent_id/run_id`：對話過濾器（至少需要一個）。
  - `limit`：最大結果數（預設：100）。
  - `filters`：額外的搜尋過濾器。
  - `threshold`：最小相似度分數。
- **回傳**：包含 "results" 的字典，內含具分數的記憶項。

**get(memory_id)**
- **用途**：根據 ID 獲取特定記憶。
- **回傳**：包含 id、memory、hash、時間戳記、元數據的記憶字典。

**get_all(*, user_id=None, agent_id=None, run_id=None, filters=None, limit=100)**
- **用途**：列出所有記憶（可選過濾條件）。
- **回傳**：包含 "results" 的字典，內含記憶列表。

**update(memory_id, data)**
- **用途**：更新記憶內容或元數據。
- **回傳**：成功訊息字典。

**delete(memory_id)**
- **用途**：刪除特定記憶。
- **回傳**：成功訊息字典。

**delete_all(user_id=None, agent_id=None, run_id=None)**
- **用途**：刪除該對話的所有記憶（至少需要一個 ID）。
- **回傳**：成功訊息字典。

**history(memory_id)**
- **用途**：獲取記憶變更歷史。
- **回傳**：記憶變更歷史列表。

**reset()**
- **用途**：重置整個記憶儲存。
- **回傳**：None

### MemoryClient 類別 (託管平台)

**匯入：** `from mem0 import MemoryClient, AsyncMemoryClient`

#### 初始化
```python
client = MemoryClient(
    api_key="your-api-key",  # 或設定 MEM0_API_KEY 環境變數
    host="https://api.mem0.ai",  # 選填
    org_id="your-org-id",  # 選填
    project_id="your-project-id"  # 選填
)
```

#### 核心方法

**add(messages, **kwargs)**
- **用途**：從對話訊息中建立記憶。
- **參數**：messages (訊息字典列表), user_id, agent_id, app_id, metadata, filters。
- **回傳**：包含記憶建立結果的 API 回應字典。

**search(query, version="v1", **kwargs)**
- **用途**：根據查詢搜尋記憶。
- **參數**：query, version ("v1"/"v2"), user_id, agent_id, app_id, top_k, filters。
- **回傳**：搜尋結果字典列表。

**get(memory_id)**
- **用途**：根據 ID 獲取特定記憶。
- **回傳**：記憶數據字典。

**get_all(version="v1", **kwargs)**
- **用途**：獲取所有記憶（含過濾）。
- **參數**：version, user_id, agent_id, app_id, top_k, page, page_size。
- **回傳**：記憶字典列表。

**update(memory_id, text=None, metadata=None)**
- **用途**：更新記憶文字或元數據。
- **回傳**：更新後的記憶數據。

**delete(memory_id)**
- **用途**：刪除特定記憶。
- **回傳**：成功回應。

**delete_all(**kwargs)**
- **用途**：刪除符合過濾條件的所有記憶。
- **回傳**：成功訊息。

#### 批次操作 (Batch Operations)

**batch_update(memories)**
- **用途**：在單次請求中更新多條記憶。
- **參數**：記憶更新物件列表。
- **回傳**：批次操作結果。

**batch_delete(memories)**
- **用途**：在單次請求中刪除多條記憶。
- **參數**：記憶物件列表。
- **回傳**：批次操作結果。

#### 使用者管理

**users()**
- **用途**：獲取所有擁有記憶的使用者、代理和對話。
- **回傳**：包含使用者/代理/對話數據的字典。

**delete_users(user_id=None, agent_id=None, app_id=None, run_id=None)**
- **用途**：刪除特定實體或所有實體。
- **回傳**：成功訊息。

**reset()**
- **用途**：透過刪除所有使用者和記憶來重置客戶端。
- **回傳**：成功訊息。

#### 額外功能

**history(memory_id)**
- **用途**：獲取記憶變更歷史。
- **回傳**：記憶變更列表。

**feedback(memory_id, feedback, **kwargs)**
- **用途**：針對記憶提供回饋。
- **回傳**：回饋回應。

**create_memory_export(schema, **kwargs)**
- **用途**：使用 JSON Schema 建立記憶導出。
- **回傳**：導出建立回應。

**get_memory_export(**kwargs)**
- **用途**：檢索導出的記憶數據。
- **回傳**：導出的數據。


## 配置系統

### MemoryConfig

```python
from mem0.configs.base import MemoryConfig

config = MemoryConfig(
    vector_store=VectorStoreConfig(provider="qdrant", config={...}),
    llm=LlmConfig(provider="openai", config={...}),
    embedder=EmbedderConfig(provider="openai", config={...}),
    graph_store=GraphStoreConfig(provider="neo4j", config={...}),  # 選填
    history_db_path="~/.mem0/history.db",
    version="v1.1",
    custom_fact_extraction_prompt="自定義提示詞...",
    custom_update_memory_prompt="自定義提示詞..."
)
```

### 支援的提供商 (Providers)

#### LLM 提供商 (支援 19 種)
- **openai** - OpenAI GPT 模型 (預設)
- **anthropic** - Claude 模型
- **gemini** - Google Gemini
- **groq** - Groq 推理
- **ollama** - 本地 Ollama 模型
- **together** - Together AI
- **aws_bedrock** - AWS Bedrock 模型
- **azure_openai** - Azure OpenAI
- **litellm** - LiteLLM 代理 (proxy)
- **deepseek** - DeepSeek 模型
- **xai** - xAI 模型
- **sarvam** - Sarvam AI
- **lmstudio** - LM Studio 本地伺服器
- **vllm** - vLLM 推理伺服器
- **langchain** - LangChain 整合
- **openai_structured** - OpenAI 結構化輸出
- **azure_openai_structured** - Azure OpenAI 結構化輸出

#### 嵌入向量 (Embedding) 提供商 (支援 10 種)
- **openai** - OpenAI 嵌入向量 (預設)
- **ollama** - Ollama 嵌入向量
- **huggingface** - HuggingFace 模型
- **azure_openai** - Azure OpenAI 嵌入向量
- **gemini** - Google Gemini 嵌入向量
- **vertexai** - Google Vertex AI
- **together** - Together AI 嵌入向量
- **lmstudio** - LM Studio 嵌入向量
- **langchain** - LangChain 嵌入向量
- **aws_bedrock** - AWS Bedrock 嵌入向量

#### 向量儲存 (Vector Store) 提供商 (支援 19 種)
- **qdrant** - Qdrant 向量資料庫 (預設)
- **chroma** - ChromaDB
- **pinecone** - Pinecone 向量資料庫
- **pgvector** - 支援 pgvector 的 PostgreSQL
- **mongodb** - MongoDB Atlas 向量搜尋
- **milvus** - Milvus 向量資料庫
- **weaviate** - Weaviate
- **faiss** - Facebook AI 相似度搜尋
- **redis** - Redis 向量搜尋
- **elasticsearch** - Elasticsearch
- **opensearch** - OpenSearch
- **azure_ai_search** - Azure AI 搜尋
- **vertex_ai_vector_search** - Google Vertex AI 向量搜尋
- **upstash_vector** - Upstash Vector
- **supabase** - Supabase 向量
- **baidu** - 百度向量資料庫
- **langchain** - LangChain 向量儲存
- **s3_vectors** - Amazon S3 向量
- **databricks** - Databricks 向量儲存

#### 圖形儲存 (Graph Store) 提供商 (支援 4 種)
- **neo4j** - Neo4j 圖形資料庫
- **memgraph** - Memgraph
- **neptune** - AWS Neptune Analytics
- **kuzu** - Kuzu 圖形資料庫

### 配置範例

#### OpenAI 配置
```python
config = MemoryConfig(
    llm={
        "provider": "openai",
        "config": {
            "model": "gpt-4.1-nano-2025-04-14",
            "temperature": 0.1,
            "max_tokens": 1000
        }
    },
    embedder={
        "provider": "openai",
        "config": {
            "model": "text-embedding-3-small"
        }
    }
)
```

#### 使用 Ollama 的本地設置
```python
config = MemoryConfig(
    llm={
        "provider": "ollama",
        "config": {
            "model": "llama3.1:8b",
            "ollama_base_url": "http://localhost:11434"
        }
    },
    embedder={
        "provider": "ollama",
        "config": {
            "model": "nomic-embed-text"
        }
    },
    vector_store={
        "provider": "chroma",
        "config": {
            "collection_name": "my_memories",
            "path": "./chroma_db"
        }
    }
)
```

#### 使用 Neo4j 的圖形記憶
```python
config = MemoryConfig(
    graph_store={
        "provider": "neo4j",
        "config": {
            "url": "bolt://localhost:7687",
            "username": "neo4j",
            "password": "password",
            "database": "neo4j"
        }
    }
)
```

#### 企業級設置
```python
config = MemoryConfig(
    llm={
        "provider": "azure_openai",
        "config": {
            "model": "gpt-4",
            "azure_endpoint": "https://your-resource.openai.azure.com/",
            "api_key": "your-api-key",
            "api_version": "2024-02-01"
        }
    },
    vector_store={
        "provider": "pinecone",
        "config": {
            "api_key": "your-pinecone-key",
            "index_name": "mem0-index",
            "dimension": 1536
        }
    }
)
```

#### LLM 提供商詳細說明
- **OpenAI** - GPT-4, GPT-3.5-turbo 及結構化輸出。
- **Anthropic** - 具備進階推理能力的 Claude 模型。
- **Google AI** - 用於多模態應用的 Gemini 模型。
- **AWS Bedrock** - 企業級 AWS 託管模型。
- **Azure OpenAI** - Microsoft Azure 託管的 OpenAI 模型。
- **Groq** - 高效能 LPU 優化模型。
- **Together** - 開源模型推理平台。
- **Ollama** - 兼顧隱私的本地模型部署。
- **vLLM** - 高效能推理框架。
- **LM Studio** - 本地模型管理。
- **DeepSeek** - 進階推理模型。
- **Sarvam** - 印度語言模型。
- **XAI** - xAI 模型。
- **LiteLLM** - 統一的 LLM 介面。
- **LangChain** - LangChain LLM 整合。

#### 向量儲存提供商詳細說明
- **Chroma** - AI 原生開源向量資料庫。
- **Qdrant** - 高效能向量相似度搜尋。
- **Pinecone** - 具備無伺服器選項的託管向量資料庫。
- **Weaviate** - 開源向量搜尋引擎。
- **PGVector** - 用於向量搜尋的 PostgreSQL 擴充功能。
- **Milvus** - 供大規模使用的開源向量資料庫。
- **Redis** - 使用 Redis Stack 的即時向量儲存。
- **Supabase** - 開源的 Firebase 替代方案。
- **Upstash Vector** - 無伺服器向量資料庫。
- **Elasticsearch** - 分散式搜尋與分析。
- **OpenSearch** - 開源搜尋與分析。
- **FAISS** - Facebook AI 相似度搜尋。
- **MongoDB** - 具備向量搜尋功能的文件資料庫。
- **Azure AI Search** - Microsoft 的搜尋服務。
- **Vertex AI Vector Search** - Google Cloud 向量搜尋。
- **Databricks Vector Search** - Delta Lake 整合。
- **百度 (Baidu)** - 百度向量資料庫。
- **LangChain** - LangChain 向量儲存整合。

#### 嵌入向量提供商詳細說明
- **OpenAI** - 高品質文字嵌入向量。
- **Azure OpenAI** - 企業級 Azure 託管嵌入向量。
- **Google AI** - Gemini 嵌入向量模型。
- **AWS Bedrock** - Amazon 嵌入向量模型。
- **Hugging Face** - 開源嵌入向量模型。
- **Vertex AI** - Google Cloud 企業級嵌入向量。
- **Ollama** - 本地嵌入向量模型。
- **Together** - 開源模型嵌入向量。
- **LM Studio** - 本地模型嵌入向量。
- **LangChain** - LangChain 嵌入向量整合。

## TypeScript/JavaScript SDK

### 客戶端 SDK (託管平台)

```typescript
import { MemoryClient } from 'mem0ai';

const client = new MemoryClient({
  apiKey: 'your-api-key',
  host: 'https://api.mem0.ai',  // 選填
  organizationId: 'org-id',     // 選填
  projectId: 'project-id'       // 選填
});

// 核心操作
const memories = await client.add([
  { role: 'user', content: '我喜歡披薩' }
], { user_id: 'user123' });

const results = await client.search('食物偏好', { user_id: 'user123' });
const memory = await client.get('memory-id');
const allMemories = await client.getAll({ user_id: 'user123' });

// 管理操作
await client.update('memory-id', '更新後的內容');
await client.delete('memory-id');
await client.deleteAll({ user_id: 'user123' });

// 批次操作
await client.batchUpdate([{ id: 'mem1', text: '新文字' }]);
await client.batchDelete(['mem1', 'mem2']);

// 使用者管理
const users = await client.users();
await client.deleteUsers({ user_ids: ['user1', 'user2'] });

// Webhooks
const webhooks = await client.getWebhooks();
await client.createWebhook({
  url: 'https://your-webhook.com',
  name: '我的 Webhook',
  eventTypes: ['memory.created', 'memory.updated']
});
```

### 開源版 (OSS) SDK (自行託管)

```typescript
import { Memory } from 'mem0ai/oss';

const memory = new Memory({
  embedder: {
    provider: 'openai',
    config: { apiKey: 'your-key' }
  },
  vectorStore: {
    provider: 'qdrant',
    config: { host: 'localhost', port: 6333 }
  },
  llm: {
    provider: 'openai',
    config: { model: 'gpt-4.1-nano' }
  }
});

// 核心操作
const result = await memory.add('我喜歡披薩', { userId: 'user123' });
const searchResult = await memory.search('食物偏好', { userId: 'user123' });
const memoryItem = await memory.get('memory-id');
const allMemories = await memory.getAll({ userId: 'user123' });

// 管理
await memory.update('memory-id', '更新後的內容');
await memory.delete('memory-id');
await memory.deleteAll({ userId: 'user123' });

// 歷史與重置
const history = await memory.history('memory-id');
await memory.reset();
```

### 關鍵 TypeScript 類型

```typescript
interface Message {
  role: 'user' | 'assistant';
  content: string | MultiModalMessages;
}

interface Memory {
  id: string;
  memory?: string;
  user_id?: string;
  categories?: string[];
  created_at?: Date;
  updated_at?: Date;
  metadata?: any;
  score?: number;
}

interface MemoryOptions {
  user_id?: string;
  agent_id?: string;
  app_id?: string;
  run_id?: string;
  metadata?: Record<string, any>;
  filters?: Record<string, any>;
  api_version?: 'v1' | 'v2';
  infer?: boolean;
  enable_graph?: boolean;
}

interface SearchResult {
  results: Memory[];
  relations?: any[];
}
```

## 進階功能

### 圖形記憶 (Graph Memory)

圖形記憶可以追蹤對話中提到的實體之間的關係。

```python
# 啟用圖形記憶
config = MemoryConfig(
    graph_store={
        "provider": "neo4j",
        "config": {
            "url": "bolt://localhost:7687",
            "username": "neo4j",
            "password": "password"
        }
    }
)
memory = Memory(config)

# 新增具備關係提取功能的記憶
result = memory.add(
    "John 在 OpenAI 工作，並且是 Sarah 的朋友",
    user_id="user123"
)

# 結果包含記憶項與關係
print(result["results"])     # 記憶項
print(result["relations"])   # 圖形關係
```

**支援的圖形資料庫：**
- **Neo4j**：具備 Cypher 查詢功能的完整圖形資料庫。
- **Memgraph**：高效能記憶體內圖形資料庫。
- **Neptune**：AWS 託管圖形資料庫服務。
- **kuzu** - 開源 Kuzu 圖形資料庫。

### 多模態記憶 (Multimodal Memory)

儲存並檢索來自文字、圖片和 PDF 的記憶。

```python
# 文字 + 圖片
messages = [
    {"role": "user", "content": "這是我的旅行設備"},
    {
        "role": "user",
        "content": {
            "type": "image_url",
            "image_url": {"url": f"data:image/jpeg;base64,{base64_image}"}
        }
    }
]
client.add(messages, user_id="user123")

# PDF 處理
pdf_message = {
    "role": "user",
    "content": {
        "type": "pdf_url",
        "pdf_url": {"url": "https://example.com/document.pdf"}
    }
}
client.add([pdf_message], user_id="user123")
```

### 程序性記憶 (Procedural Memory)

儲存步驟化的程序與工作流程。

```python
# 新增程序性記憶
result = memory.add(
    "部署應用程式的步驟：1. 執行測試 2. 建立 Docker 映像檔 3. 推送到登錄庫 4. 更新 k8s 清單",
    user_id="developer123",
    memory_type="procedural_memory"
)

# 搜尋程序
procedures = memory.search(
    "如何部署？",
    user_id="developer123"
)
```

### 自定義提示詞 (Custom Prompts)

```python
custom_extraction_prompt = """
從對話中提取關鍵事實，重點關注：
1. 個人偏好
2. 技術技能
3. 專案需求
4. 重要日期與截止期限

對話內容： {messages}
"""

config = MemoryConfig(
    custom_fact_extraction_prompt=custom_extraction_prompt
)
memory = Memory(config)
```


## 常見使用模式

### 1. 個人 AI 助手

```python
class PersonalAssistant:
    def __init__(self):
        self.memory = Memory()
        self.llm = OpenAI()  # 您的 LLM 客戶端
    
    def chat(self, user_input: str, user_id: str) -> str:
        # 檢索相關記憶
        memories = self.memory.search(user_input, user_id=user_id, limit=5)
        
        # 從記憶建立上下文
        context = "
".join([f"- {m['memory']}" for m in memories['results']])
        
        # 產生具備上下文的回應
        prompt = f"""
        來自先前對話的上下文：
        {context}
        
        使用者： {user_input}
        助手：
        """
        
        response = self.llm.generate(prompt)
        
        # 儲存對話
        self.memory.add([
            {"role": "user", "content": user_input},
            {"role": "assistant", "content": response}
        ], user_id=user_id)
        
        return response
```

### 2. 客戶支援機器人

```python
class SupportBot:
    def __init__(self):
        self.memory = MemoryClient(api_key="your-key")
    
    def handle_ticket(self, customer_id: str, issue: str) -> str:
        # 獲取客戶歷史記錄
        history = self.memory.search(
            issue,
            user_id=customer_id,
            limit=10
        )
        
        # 檢查是否有類似的過往問題
        similar_issues = [m for m in history if m['score'] > 0.8]
        
        if similar_issues:
            context = f"先前類似的問題： {similar_issues[0]['memory']}"
        else:
            context = "未找到先前類似的問題。"
        
        # 產生回應
        response = self.generate_support_response(issue, context)
        
        # 儲存互動
        self.memory.add([
            {"role": "user", "content": f"問題： {issue}"},
            {"role": "assistant", "content": response}
        ], user_id=customer_id, metadata={
            "category": "support_ticket",
            "timestamp": datetime.now().isoformat()
        })
        
        return response
```

### 3. 學習助手

```python
class StudyBuddy:
    def __init__(self):
        self.memory = Memory()
    
    def study_session(self, student_id: str, topic: str, content: str):
        # 儲存學習教材
        self.memory.add(
            f"學習了 {topic}： {content}",
            user_id=student_id,
            metadata={
                "topic": topic,
                "session_date": datetime.now().isoformat(),
                "type": "study_session"
            }
        )
    
    def quiz_student(self, student_id: str, topic: str) -> list:
        # 獲取相關學習教材
        materials = self.memory.search(
            f"topic:{topic}",
            user_id=student_id,
            filters={"metadata.type": "study_session"}
        )
        
        # 根據教材產生測驗題目
        questions = self.generate_quiz_questions(materials)
        return questions
    
    def track_progress(self, student_id: str) -> dict:
        # 獲取所有學習環節
        sessions = self.memory.get_all(
            user_id=student_id,
            filters={"metadata.type": "study_session"}
        )
        
        # 分析進度
        topics_studied = {}
        for session in sessions['results']:
            topic = session['metadata']['topic']
            topics_studied[topic] = topics_studied.get(topic, 0) + 1
        
        return {
            "total_sessions": len(sessions['results']),
            "topics_covered": len(topics_studied),
            "topic_frequency": topics_studied
        }
```

### 4. 多代理系統 (Multi-Agent System)

```python
class MultiAgentSystem:
    def __init__(self):
        self.shared_memory = Memory()
        self.agents = {
            "researcher": ResearchAgent(),
            "writer": WriterAgent(),
            "reviewer": ReviewAgent()
        }
    
    def collaborative_task(self, task: str, session_id: str):
        # 研究階段
        research_results = self.agents["researcher"].research(task)
        self.shared_memory.add(
            f"研究發現： {research_results}",
            agent_id="researcher",
            run_id=session_id,
            metadata={"phase": "research"}
        )
        
        # 寫作階段
        research_context = self.shared_memory.search(
            "研究發現",
            run_id=session_id
        )
        draft = self.agents["writer"].write(task, research_context)
        self.shared_memory.add(
            f"草稿內容： {draft}",
            agent_id="writer",
            run_id=session_id,
            metadata={"phase": "writing"}
        )
        
        # 審閱階段
        all_context = self.shared_memory.get_all(run_id=session_id)
        final_output = self.agents["reviewer"].review(draft, all_context)
        
        return final_output
```

### 5. 具備記憶能力的語音助手

```python
import speech_recognition as sr
from gtts import gTTS
import pygame

class VoiceAssistant:
    def __init__(self):
        self.memory = Memory()
        self.recognizer = sr.Recognizer()
        self.microphone = sr.Microphone()
    
    def listen_and_respond(self, user_id: str):
        # 聆聽使用者
        with self.microphone as source:
            audio = self.recognizer.listen(source)
        
        try:
            # 將語音轉換為文字
            user_input = self.recognizer.recognize_google(audio)
            print(f"使用者說： {user_input}")
            
            # 獲取相關記憶
            memories = self.memory.search(user_input, user_id=user_id)
            context = "
".join([m['memory'] for m in memories['results'][:3]])
            
            # 產生回應
            response = self.generate_response(user_input, context)
            
            # 儲存對話
            self.memory.add([
                {"role": "user", "content": user_input},
                {"role": "assistant", "content": response}
            ], user_id=user_id)
            
            # 將回應轉換為語音
            tts = gTTS(text=response, lang='en')
            tts.save("response.mp3")
            
            # 播放回應
            pygame.mixer.init()
            pygame.mixer.music.load("response.mp3")
            pygame.mixer.music.play()
            
            return response
            
        except sr.UnknownValueError:
            return "抱歉，我沒聽懂。"
```

## 最佳實踐

### 1. 記憶組織

```python
# 使用一致的使用者/代理/對話 ID
user_id = f"user_{user_email.replace('@', '_')}"
agent_id = f"agent_{agent_name}"
run_id = f"session_{datetime.now().strftime('%Y%m%d_%H%M%S')}"

# 新增具備意義的元數據
metadata = {
    "category": "customer_support",
    "priority": "high",
    "department": "technical",
    "timestamp": datetime.now().isoformat(),
    "source": "chat_widget"
}

# 使用描述性的記憶內容
memory.add(
    "客戶 John Smith 回報了行動裝置應用程式上的 2FA 登入問題。已透過清除應用程式快取解決。",
    user_id=customer_id,
    metadata=metadata
)
```

### 2. 搜尋優化

```python
# 使用特定的搜尋查詢
results = memory.search(
    "行動裝置應用程式登入問題",  # 特定關鍵字
    user_id=customer_id,
    limit=5,  # 合理的限制
    threshold=0.7  # 過濾低相關度結果
)

# 結合多次搜尋以獲得全面結果
technical_issues = memory.search("技術問題", user_id=user_id)
recent_conversations = memory.get_all(
    user_id=user_id,
    filters={"metadata.timestamp": {"$gte": last_week}},
    limit=10
)
```

### 3. 記憶生命週期管理

```python
# 定期清理舊記憶
def cleanup_old_memories(memory_client, days_old=90):
    cutoff_date = datetime.now() - timedelta(days=days_old)
    
    all_memories = memory_client.get_all()
    for mem in all_memories:
        if datetime.fromisoformat(mem['created_at']) < cutoff_date:
            memory_client.delete(mem['id'])

# 封存重要記憶
def archive_memory(memory_client, memory_id):
    memory = memory_client.get(memory_id)
    memory_client.update(memory_id, metadata={
        **memory.get('metadata', {}),
        'archived': True,
        'archive_date': datetime.now().isoformat()
    })
```

### 4. 錯誤處理

```python
def safe_memory_operation(memory_client, operation, *args, **kwargs):
    try:
        return operation(*args, **kwargs)
    except Exception as e:
        logger.error(f"記憶操作失敗： {e}")
        # 回退到無記憶的基本回應
        return {"results": [], "message": "記憶暫時無法使用"}

# 使用方式
results = safe_memory_operation(
    memory_client,
    memory_client.search,
    query,
    user_id=user_id
)
```

### 5. 效能優化

```python
# 盡可能使用批次操作
memories_to_add = [
    {"content": msg1, "user_id": user_id},
    {"content": msg2, "user_id": user_id},
    {"content": msg3, "user_id": user_id}
]

# 與其多次呼叫 add()，不如使用批次操作
for memory_data in memories_to_add:
    memory.add(memory_data["content"], user_id=memory_data["user_id"])

# 快取頻繁存取的記憶
from functools import lru_cache

@lru_cache(maxsize=100)
def get_user_preferences(user_id: str):
    return memory.search("偏好設定", user_id=user_id, limit=5)
```


## 整合範例

### AutoGen 整合

```python
from cookbooks.helper.mem0_teachability import Mem0Teachability
from mem0 import Memory

# 為 AutoGen 代理新增記憶能力
memory = Memory()
teachability = Mem0Teachability(
    verbosity=1,
    reset_db=False,
    recall_threshold=1.5,
    memory_client=memory
)

# 套用到代理
teachability.add_to_agent(your_autogen_agent)
```

### LangChain 整合

```python
from langchain.memory import ConversationBufferMemory
from mem0 import Memory

class Mem0LangChainMemory(ConversationBufferMemory):
    def __init__(self, user_id: str, **kwargs):
        super().__init__(**kwargs)
        self.mem0 = Memory()
        self.user_id = user_id
    
    def save_context(self, inputs, outputs):
        # 同時儲存到 LangChain 和 mem0
        super().save_context(inputs, outputs)
        
        # 儲存在 mem0 作為長期記憶
        self.mem0.add([
            {"role": "user", "content": str(inputs)},
            {"role": "assistant", "content": str(outputs)}
        ], user_id=self.user_id)
    
    def load_memory_variables(self, inputs):
        # 從 LangChain 緩衝區載入
        variables = super().load_memory_variables(inputs)
        
        # 使用相關的長期記憶進行增強
        relevant_memories = self.mem0.search(
            str(inputs),
            user_id=self.user_id,
            limit=3
        )
        
        if relevant_memories['results']:
            long_term_context = "
".join([
                f"- {m['memory']}" for m in relevant_memories['results']
            ])
            variables['history'] += f"

相關的過往上下文：
{long_term_context}"
        
        return variables
```

### Streamlit 應用程式

```python
import streamlit as st
from mem0 import Memory

# 初始化記憶
if 'memory' not in st.session_state:
    st.session_state.memory = Memory()

# 使用者輸入
user_id = st.text_input("使用者 ID", value="user123")
user_message = st.text_input("您的訊息")

if st.button("發送"):
    # 獲取相關記憶
    memories = st.session_state.memory.search(
        user_message,
        user_id=user_id,
        limit=5
    )
    
    # 顯示記憶
    if memories['results']:
        st.subheader("相關記憶：")
        for memory in memories['results']:
            st.write(f"- {memory['memory']} (分數： {memory['score']:.2f})")
    
    # 產生並顯示回應
    response = generate_response(user_message, memories)
    st.write(f"助手： {response}")
    
    # 儲存對話
    st.session_state.memory.add([
        {"role": "user", "content": user_message},
        {"role": "assistant", "content": response}
    ], user_id=user_id)

# 顯示所有記憶
if st.button("顯示所有記憶"):
    all_memories = st.session_state.memory.get_all(user_id=user_id)
    for memory in all_memories['results']:
        st.write(f"- {memory['memory']}")
```

### FastAPI 後端

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from mem0 import MemoryClient
from typing import List, Optional

app = FastAPI()
memory_client = MemoryClient(api_key="your-api-key")

class ChatMessage(BaseModel):
    role: str
    content: str

class ChatRequest(BaseModel):
    messages: List[ChatMessage]
    user_id: str
    metadata: Optional[dict] = None

class SearchRequest(BaseModel):
    query: str
    user_id: str
    limit: int = 10

@app.post("/chat")
async def chat(request: ChatRequest):
    try:
        # 將訊息新增至記憶
        result = memory_client.add(
            [msg.dict() for msg in request.messages],
            user_id=request.user_id,
            metadata=request.metadata
        )
        return {"status": "success", "result": result}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.post("/search")
async def search_memories(request: SearchRequest):
    try:
        results = memory_client.search(
            request.query,
            user_id=request.user_id,
            limit=request.limit
        )
        return {"results": results}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.get("/memories/{user_id}")
async def get_user_memories(user_id: str, limit: int = 50):
    try:
        memories = memory_client.get_all(user_id=user_id, limit=limit)
        return {"memories": memories}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.delete("/memories/{memory_id}")
async def delete_memory(memory_id: str):
    try:
        result = memory_client.delete(memory_id)
        return {"status": "deleted", "result": result}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

## 故障排除 (Troubleshooting)

### 常見問題

1. **找不到記憶 (Memory Not Found)**
   ```python
   # 在操作前檢查記憶是否存在
   memory = memory_client.get(memory_id)
   if not memory:
       print(f"找不到記憶 {memory_id}")
   ```

2. **搜尋沒有結果**
   ```python
   # 降低相似度閾值
   results = memory.search(
       query,
       user_id=user_id,
       threshold=0.5  # 較低的閾值
   )
   
   # 檢查該使用者是否存在記憶
   all_memories = memory.get_all(user_id=user_id)
   if not all_memories['results']:
       print("未找到該使用者的記憶")
   ```

3. **配置問題**
   ```python
   # 驗證配置
   try:
       memory = Memory(config)
       # 進行簡單的操作測試
       memory.add("測試記憶", user_id="test")
       print("配置有效")
   except Exception as e:
       print(f"配置錯誤： {e}")
   ```

4. **API 速率限制 (Rate Limits)**
   ```python
   import time
   from functools import wraps
   
   def rate_limit_retry(max_retries=3, delay=1):
       def decorator(func):
           @wraps(func)
           def wrapper(*args, **kwargs):
               for attempt in range(max_retries):
                   try:
                       return func(*args, **kwargs)
                   except Exception as e:
                       if "rate limit" in str(e).lower() and attempt < max_retries - 1:
                           time.sleep(delay * (2 ** attempt))  # 指數退避 (Exponential backoff)
                           continue
                       raise e
               return wrapper
           return decorator
   
   @rate_limit_retry()
   def safe_memory_add(memory, content, user_id):
       return memory.add(content, user_id=user_id)
   ```

### 效能提示

1. **優化向量儲存配置**
   ```python
   # 以 Qdrant 為例
   config = MemoryConfig(
       vector_store={
           "provider": "qdrant",
           "config": {
               "host": "localhost",
               "port": 6333,
               "collection_name": "memories",
               "embedding_model_dims": 1536,
               "distance": "cosine"
           }
       }
   )
   ```

2. **批次處理**
   ```python
   # 高效處理多條記憶
   def batch_add_memories(memory_client, conversations, user_id, batch_size=10):
       for i in range(0, len(conversations), batch_size):
           batch = conversations[i:i+batch_size]
           for conv in batch:
               memory_client.add(conv, user_id=user_id)
           time.sleep(0.1)  # 批次之間的小延遲
   ```

3. **記憶清理**
   ```python
   # 定期清理以維持效能
   def cleanup_memories(memory_client, user_id, max_memories=1000):
       all_memories = memory_client.get_all(user_id=user_id)
       if len(all_memories) > max_memories:
           # 保留最近的記憶
           sorted_memories = sorted(
               all_memories,
               key=lambda x: x['created_at'],
               reverse=True
           )
           
           # 刪除最舊的記憶
           for memory in sorted_memories[max_memories:]:
               memory_client.delete(memory['id'])
   ```

## 資源

- **文件**： https://docs.mem0.ai
- **GitHub 儲存庫**： https://github.com/mem0ai/mem0
- **Discord 社群**： https://mem0.dev/DiG
- **平台**： https://app.mem0.ai
- **研究論文**： https://mem0.ai/research
- **範例**： https://github.com/mem0ai/mem0/tree/main/examples

## 授權協議

mem0 採用 Apache 2.0 授權。詳情請參閱 [LICENSE](https://github.com/mem0ai/mem0/blob/main/LICENSE) 檔案。
