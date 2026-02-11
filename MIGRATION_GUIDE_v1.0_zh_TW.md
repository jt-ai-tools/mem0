# 遷移指南：升級至 mem0 1.0.0

## 懶人包 (TL;DR)

**有什麼變動？** 我們透過移除混淆的版本參數簡化了 API。現在所有回應都採用一致的格式：`{"results": [...]}`。

**您需要做什麼：**
1. 升級：`pip install mem0ai==1.0.0`
2. 從程式碼中移除 `version` 和 `output_format` 參數。
3. 更新回應處理邏輯，使用 `result["results"]` 而非將回應視為列表。

**所需時間：** 大多數專案約需 5-10 分鐘。

---

## 快速遷移指南

### 1. 安裝更新

```bash
pip install mem0ai==1.0.0
```

### 2. 更新您的程式碼

**如果您使用的是 Memory API：**

```python
# 之前
memory = Memory(config=MemoryConfig(version="v1.1"))
result = memory.add("我喜歡披薩")

# 之後
memory = Memory()  # 就這樣 - 現在版本是自動處理的
result = memory.add("我喜歡披薩")
```

**如果您使用的是 Client API：**

```python
# 之前
client.add(messages, output_format="v1.1")
client.search(query, version="v2", output_format="v1.1")

# 之後
client.add(messages)  # 移除這些額外參數即可
client.search(query)
```

### 3. 更新回應處理方式

所有回應現在都使用相同的格式：一個包含 `"results"` 鍵的字典。

```python
# 之前 - 您可能這樣寫
result = memory.add("我喜歡披薩")
for item in result:  # 將其視為列表
    print(item)

# 之後 - 請改用這種方式
result = memory.add("我喜歡披薩")
for item in result["results"]:  # 存取 results 鍵
    print(item)

# 圖形關係 (如果您有使用的話)
if "relations" in result:
    for relation in result["relations"]:
        print(relation)
```

---

## 增強的訊息處理

平台客戶端 (MemoryClient) 現在支援與開源 (OSS) 版本同樣靈活的訊息格式：

```python
from mem0 import MemoryClient

client = MemoryClient(api_key="your-key")

# 現在支援以下三種格式：

# 1. 單一字串（自動轉換為使用者訊息）
client.add("我喜歡披薩", user_id="alice")

# 2. 單一訊息字典
client.add({"role": "user", "content": "我喜歡披薩"}, user_id="alice")

# 3. 訊息列表（對話）
client.add([
    {"role": "user", "content": "我喜歡披薩"},
    {"role": "assistant", "content": "我會記住這一點！"}
], user_id="alice")
```

### 非同步模式配置 (Async Mode Configuration)

`async_mode` 參數現在預設為 `True`，但可以進行配置：

```python
# 預設行為 (async_mode=True)
client.add(messages, user_id="alice")

# 明確設定非同步模式
client.add(messages, user_id="alice", async_mode=True)

# 若有需要，可以停用非同步模式
client.add(messages, user_id="alice", async_mode=False)
```

**注意：** 在大多數使用案例中，`async_mode=True` 能提供更好的效能。只有在您有特定的同步處理需求時，才將其設為 `False`。

---

## 就這樣！

對於大多數使用者來說，這就是您需要了解的所有內容。變動摘要如下：
- ✅ 不再需要 `version` 或 `output_format` 參數。
- ✅ 一致的 `{"results": [...]}` 回應格式。
- ✅ 更簡潔、更直觀的 API。

---

## 常見問題

**遇到 `KeyError: 'results'`？**

您的程式碼仍將回應視為列表。請更新如下：
```python
# 將此程式碼：
for memory in response:

# 修改為：
for memory in response["results"]:
```

**遇到 `TypeError: unexpected keyword argument`？**

您仍在傳遞舊參數。請移除它們：
```python
# 將此程式碼：
client.add(messages, output_format="v1.1")

# 修改為：
client.add(messages)
```

**看到棄用 (Deprecation) 警告？**

從配置中移除任何明確的 `version="v1.0"`：
```python
# 將此程式碼：
memory = Memory(config=MemoryConfig(version="v1.0"))

# 修改為：
memory = Memory()
```

---

## 1.0.0 的新功能

- **更好的向量儲存**：修正了 OpenSearch 問題並提升了所有儲存空間的可靠性。
- **更簡潔的 API**：統一的操作方式，不再有混淆的選項。
- **增強的 GCP 支援**：更好的 Vertex AI 配置選項。
- **靈活的訊息輸入**：平台客戶端現在接受字串、字典和列表（與開源版一致）。
- **可配置的 async_mode**：現在預設為 `True`，但使用者可視需求覆寫。

---

## 需要協助？

- 查看 [GitHub Issues](https://github.com/mem0ai/mem0/issues)
- 閱讀 [說明文件](https://docs.mem0.ai/)
- 如果卡住了，請開立新的 Issue。

---

## 進階：配置變動

**如果您曾針對向量儲存配置版本：**

```python
# 之前
config = MemoryConfig(
    version="v1.1",
    vector_store=VectorStoreConfig(...)
)

# 之後
config = MemoryConfig(
    vector_store=VectorStoreConfig(...)
)
```

---

## 測試您的遷移結果

快速檢查：

```python
from mem0 import Memory

memory = Memory()

# 新增操作應回傳一個包含 "results" 的字典
result = memory.add("我喜歡披薩", user_id="test")
assert "results" in result

# 搜尋操作應回傳一個包含 "results" 的字典
search = memory.search("食物", user_id="test")
assert "results" in search

# 獲取所有記憶應回傳一個包含 "results" 的字典
all_memories = memory.get_all(user_id="test")
assert "results" in all_memories

print("✅ 遷移成功！")
```
