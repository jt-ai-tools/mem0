# mem0：構建具備可擴展長期記憶的生產級 AI 代理

[![arXiv](https://img.shields.io/badge/arXiv-Paper-b31b1b.svg)](https://arxiv.org/abs/2504.19413)
[![Website](https://img.shields.io/badge/Website-Project-blue)](https://mem0.ai/research)

本儲存庫包含我們論文的程式碼與數據集：**Mem0: Building Production‑Ready AI Agents with Scalable Long‑Term Memory**。

## 📋 總覽

本專案對 mem0 進行評估，並將其與 AI 系統的其他記憶與檢索技術進行比較：

1. **成熟的 LOCOMO 基準測試**：我們針對文獻中五種成熟的方法進行評估：LoCoMo、ReadAgent、MemoryBank、MemGPT 和 A-Mem。
2. **開源記憶解決方案**：我們測試了具備潛力的開源記憶架構，包括提供靈活記憶管理能力的 LangMem。
3. **RAG 系統**：我們實作了多種配置的檢索增強生成 (Retrieval-Augmented Generation)，測試不同的區塊大小 (Chunk size) 與檢索數量以優化效能。
4. **全上下文處理 (Full-Context Processing)**：我們檢驗了將整個對話歷史傳遞到 LLM 上下文視窗中作為基準方法的有效性。
5. **專有記憶系統**：我們評估了 OpenAI 在 ChatGPT 介面中提供的內建記憶功能，以與商用解決方案進行比較。
6. **第三方記憶提供商**：我們納入了 Zep（一個專為 AI 代理設計的專業記憶管理平台），以評估專用記憶基礎設施的效能。

我們在 LOCOMO 數據集上測試這些技術，該數據集包含多種問題類型的對話數據，用於評估記憶召回與理解能力。

## 🔍 數據集

實驗中使用的 LOCOMO 數據集可從我們的 Google Drive 儲存庫下載：

[下載 LOCOMO 數據集](https://drive.google.com/drive/folders/1L-cTjTm0ohMsitsHg4dijSPJtqNflwX-?usp=drive_link)

該數據集包含專為測試各種問題類型與複雜程度下的記憶召回與理解而設計的對話數據。

請將數據集檔案放置在 `dataset/` 目錄中：
- `locomo10.json`：原始數據集
- `locomo10_rag.json`：為 RAG 實驗格式化的數據集

## 📁 專案結構

```
.
├── src/                  # 各種記憶技術的原始碼
│   ├── mem0/             # mem0 技術的實作
│   ├── openai/           # OpenAI 記憶的實作
│   ├── zep/              # Zep 記憶的實作
│   ├── rag.py            # RAG 技術的實作
│   └── langmem.py        # 基於語言的記憶實作
├── metrics/              # 評估指標的程式碼
├── results/              # 實驗結果
├── dataset/              # 數據集檔案
├── evals.py              # 評估指令碼
├── run_experiments.py    # 執行實驗的指令碼
├── generate_scores.py    # 從結果生成分數的指令碼
└── prompts.py            # 模型使用的提示詞
```

## 🚀 開始使用

### 前置需求

建立一個包含您的 API 金鑰與配置的 `.env` 檔案。需要以下金鑰：

```
# 用於 GPT 模型與嵌入向量的 OpenAI API 金鑰
OPENAI_API_KEY="您的-openai-api-金鑰"

# mem0 API 金鑰（用於 mem0 與 mem0+ 技術）
MEM0_API_KEY="您的-mem0-api-金鑰"
MEM0_PROJECT_ID="您的-mem0-專案-id"
MEM0_ORGANIZATION_ID="您的-mem0-組織-id"

# 模型配置
MODEL="gpt-4o-mini"  # 或您偏好的模型
EMBEDDING_MODEL="text-embedding-3-small"  # 或您偏好的嵌入向量模型
ZEP_API_KEY="來自-zep-的-api-金鑰"
```

### 執行實驗

您可以使用提供的 Makefile 指令來執行實驗：

#### 記憶技術

```bash
# 執行 mem0 實驗
make run-mem0-add         # 使用 mem0 新增記憶
make run-mem0-search      # 使用 mem0 搜尋記憶

# 執行 mem0+ 實驗（具備基於圖形的搜尋）
make run-mem0-plus-add    # 使用 mem0+ 新增記憶
make run-mem0-plus-search # 使用 mem0+ 搜尋記憶

# 執行 RAG 實驗
make run-rag              # 執行區塊大小為 500 的 RAG
make run-full-context     # 執行全上下文 RAG

# 執行 LangMem 實驗
make run-langmem          # 執行 LangMem

# 執行 Zep 實驗
make run-zep-add          # 使用 Zep 新增記憶
make run-zep-search       # 使用 Zep 搜尋記憶

# 執行 OpenAI 實驗
make run-openai           # 執行 OpenAI 實驗
```

或者，您可以直接使用自定義參數執行實驗：

```bash
python run_experiments.py --technique_type [mem0|rag|langmem] [額外參數]
```

#### 命令列參數：

| 參數 | 描述 | 預設值 |
|-----------|-------------|---------|
| `--technique_type` | 要使用的記憶技術 (mem0, rag, langmem) | mem0 |
| `--method` | 要使用的方法 (add, search) | add |
| `--chunk_size` | 處理的區塊大小 | 1000 |
| `--top_k` | 要檢索的頂尖記憶數量 | 30 |
| `--filter_memories` | 是否過濾記憶 | False |
| `--is_graph` | 是否使用基於圖形的搜尋 | False |
| `--num_chunks` | RAG 處理的區塊數量 | 1 |

### 📊 評估

若要評估結果，請執行：

```bash
python evals.py --input_file [結果路徑] --output_file [輸出路徑]
```

此指令碼將：
1. 處理每個問答對。
2. 自動計算 BLEU 與 F1 分數。
3. 使用 LLM 裁判來評估答案的正確性。
4. 將合併後的結果儲存至輸出檔案。

### 📈 生成分數

使用以下指令生成最終分數：

```bash
python generate_scores.py
```

此指令碼將：
1. 載入評估指標數據。
2. 計算每個類別的平均分數（BLEU, F1, LLM）。
3. 報告每個類別的問題數量。
4. 計算所有類別的總體平均分數。

輸出範例：
```
Mean Scores Per Category:
         bleu_score  f1_score  llm_score  count
category                                       
1           0.xxxx    0.xxxx     0.xxxx     xx
2           0.xxxx    0.xxxx     0.xxxx     xx
3           0.xxxx    0.xxxx     0.xxxx     xx

Overall Mean Scores:
bleu_score    0.xxxx
f1_score      0.xxxx
llm_score     0.xxxx
```

## 📏 評估指標

我們使用多種指標來評估不同記憶技術的效能：

1. **BLEU 分數**：測量模型回應與標準答案之間的相似度。
2. **F1 分數**：測量精準率 (Precision) 與召回率 (Recall) 的調和平均數。
3. **LLM 分數**：由 LLM 裁判評估回應正確性後確定的二元分數（0 或 1）。
4. **提示詞消耗 (Token Consumption)**：生成最終答案所需的提示詞數量。
5. **延遲 (Latency)**：搜尋與生成回應所需的時間。

## 📚 引用

如果您在研究中使用此程式碼或數據集，請引用我們的論文：

```bibtex
@article{mem0,
  title={Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory},
  author={Chhikara, Prateek and Khant, Dev and Aryan, Saket and Singh, Taranjeet and Yadav, Deshraj},
  journal={arXiv preprint arXiv:2504.19413},
  year={2025}
}
```

## 📄 授權協議

[MIT 授權協議](LICENSE)

## 👥 貢獻者

- [Prateek Chhikara](https://github.com/prateekchhikara)
- [Dev Khant](https://github.com/Dev-Khant)
- [Saket Aryan](https://github.com/whysosaket)
- [Taranjeet Singh](https://github.com/taranjeet)
- [Deshraj Yadav](https://github.com/deshraj)
