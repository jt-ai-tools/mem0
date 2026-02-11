<p align="center">
  <a href="https://github.com/mem0ai/mem0">
    <img src="docs/images/banner-sm.png" width="800px" alt="Mem0 - 為個人化 AI 打造的記憶層">
  </a>
</p>
<p align="center" style="display: flex; justify-content: center; gap: 20px; align-items: center;">
  <a href="https://trendshift.io/repositories/11194" target="blank">
    <img src="https://trendshift.io/api/badge/repositories/11194" alt="mem0ai%2Fmem0 | Trendshift" width="250" height="55"/>
  </a>
</p>

<p align="center">
  <a href="https://mem0.ai">了解更多</a>
  ·
  <a href="https://mem0.dev/DiG">加入 Discord</a>
  ·
  <a href="https://mem0.dev/demo">Demo 演示</a>
  ·
  <a href="https://mem0.dev/openmemory">OpenMemory</a>
</p>

<p align="center">
  <a href="https://mem0.dev/DiG">
    <img src="https://img.shields.io/badge/Discord-%235865F2.svg?&logo=discord&logoColor=white" alt="Mem0 Discord">
  </a>
  <a href="https://pepy.tech/project/mem0ai">
    <img src="https://img.shields.io/pypi/dm/mem0ai" alt="Mem0 PyPI - Downloads">
  </a>
  <a href="https://github.com/mem0ai/mem0">
    <img src="https://img.shields.io/github/commit-activity/m/mem0ai/mem0?style=flat-square" alt="GitHub commit activity">
  </a>
  <a href="https://pypi.org/project/mem0ai" target="blank">
    <img src="https://img.shields.io/pypi/v/mem0ai?color=%2334D058&label=pypi%20package" alt="Package version">
  </a>
  <a href="https://www.npmjs.com/package/mem0ai" target="blank">
    <img src="https://img.shields.io/npm/v/mem0ai" alt="Npm package">
  </a>
  <a href="https://www.ycombinator.com/companies/mem0">
    <img src="https://img.shields.io/badge/Y%20Combinator-S24-orange?style=flat-square" alt="Y Combinator S24">
  </a>
</p>

<p align="center">
  <a href="https://mem0.ai/research"><strong>📄 Building Production-Ready AI Agents with Scalable Long-Term Memory →</strong></a>
</p>
<p align="center">
  <strong>⚡ 準確度比 OpenAI Memory 高出 +26% • 🚀 速度快 91% • 💰 節省 90% 提示詞 (Tokens)</strong>
</p>

> **🎉 mem0ai v1.0.0 正式釋出！** 此重大版本包含 API 現代化、改進的向量儲存支援以及增強的 GCP 整合。[查看遷移指南 →](MIGRATION_GUIDE_v1.0_zh_TW.md)

## 🔥 研究亮點
- 在 LOCOMO 基準測試中，**準確度比 OpenAI Memory 高出 +26%**
- **回應速度比全上下文快 91%**，確保大規模應用下的低延遲
- **提示詞 (Token) 使用量比全上下文低 90%**，在不犧牲品質的情況下降低成本
- [閱讀完整論文](https://mem0.ai/research)

# 簡介

[Mem0](https://mem0.ai) ("mem-zero") 為 AI 助手和代理提供智慧記憶層，實現個人化的 AI 互動。它能記住使用者偏好、適應個人需求並隨時間持續學習，非常適合用於客戶支援聊天機器人、AI 助手和自主系統。

### 核心功能與應用場景

**核心能力：**
- **多層級記憶**：透過自適應個人化無縫保留使用者 (User)、對話 (Session) 和代理 (Agent) 狀態。
- **開發者友善**：直觀的 API、跨平台 SDK 以及完全託管的服務選項。

**應用場景：**
- **AI 助手**：實現一致且具備豐富上下文的對話。
- **客戶支援**：回顧過往的工單和使用者歷史記錄，提供量身定制的協助。
- **醫療保健**：追蹤病患偏好與歷史記錄，實現個人化照護。
- **生產力與遊戲**：根據使用者行為提供自適應的工作流程與環境。

## 🚀 快速上手指南 <a name="quickstart"></a>

您可以選擇使用我們的託管平台或自行託管套件：

### 託管平台

只需幾分鐘即可開始使用，享有自動更新、分析功能和企業級安全性。

1. 在 [Mem0 平台](https://app.mem0.ai) 註冊
2. 透過 SDK 或 API 金鑰嵌入記憶層

### 自行託管 (開源版)

透過 pip 安裝 SDK：

```bash
pip install mem0ai
```

透過 npm 安裝 SDK：
```bash
npm install mem0ai
```

### 基本用法

mem0 需要 LLM 才能運作，預設使用 OpenAI 的 `gpt-4.1-nano-2025-04-14`。不過，它支援多種 LLM；詳情請參閱我們的 [支援的 LLM 文件](https://docs.mem0.ai/components/llms/overview)。

第一步是實例化記憶：

```python
from openai import OpenAI
from mem0 import Memory

openai_client = OpenAI()
memory = Memory()

def chat_with_memories(message: str, user_id: str = "default_user") -> str:
    # 檢索相關記憶
    relevant_memories = memory.search(query=message, user_id=user_id, limit=3)
    memories_str = "
".join(f"- {entry['memory']}" for entry in relevant_memories["results"])

    # 生成助手回應
    system_prompt = f"You are a helpful AI. Answer the question based on query and memories.
User Memories:
{memories_str}"
    messages = [{"role": "system", "content": system_prompt}, {"role": "user", "content": message}]
    response = openai_client.chat.completions.create(model="gpt-4.1-nano-2025-04-14", messages=messages)
    assistant_response = response.choices[0].message.content

    # 從對話中建立新記憶
    messages.append({"role": "assistant", "content": assistant_response})
    memory.add(messages, user_id=user_id)

    return assistant_response

def main():
    print("Chat with AI (type 'exit' to quit)")
    while True:
        user_input = input("You: ").strip()
        if user_input.lower() == 'exit':
            print("Goodbye!")
            break
        print(f"AI: {chat_with_memories(user_input)}")

if __name__ == "__main__":
    main()
```

有關詳細的整合步驟，請參閱 [快速上手](https://docs.mem0.ai/quickstart) 和 [API 參考](https://docs.mem0.ai/api-reference)。

## 🔗 整合與演示

- **具備記憶功能的 ChatGPT**：由 mem0 驅動的個人化聊天 ([線上 Demo](https://mem0.dev/demo))
- **瀏覽器擴充功能**：在 ChatGPT、Perplexity 和 Claude 中跨平台儲存記憶 ([Chrome 線上應用程式商店](https://chromewebstore.google.com/detail/onihkkbipkfeijkadecaafbgagkhglop?utm_source=item-share-cb))
- **Langgraph 支援**：使用 Langgraph + mem0 建立客戶服務機器人 ([指南](https://docs.mem0.ai/integrations/langgraph))
- **CrewAI 整合**：使用 mem0 量身定制 CrewAI 的輸出 ([範例](https://docs.mem0.ai/integrations/crewai))

## 📚 文件與支援

- 完整文件：https://docs.mem0.ai
- 社群：[Discord](https://mem0.dev/DiG) · [Twitter](https://x.com/mem0ai)
- 聯絡我們：founders@mem0.ai

## 引用 (Citation)

我們現在有一篇論文可供引用：

```bibtex
@article{mem0,
  title={Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory},
  author={Chhikara, Prateek and Khant, Dev and Aryan, Saket and Singh, Taranjeet and Yadav, Deshraj},
  journal={arXiv preprint arXiv:2504.19413},
  year={2025}
}
```

## ⚖️ 授權協議

Apache 2.0 — 詳情請參閱 [LICENSE](https://github.com/mem0ai/mem0/blob/main/LICENSE) 檔案。
