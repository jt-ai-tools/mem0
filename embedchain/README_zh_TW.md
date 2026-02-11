<p align="center">
  <img src="docs/logo/dark.svg" width="400px" alt="Embedchain Logo">
</p>

<p align="center">
  <a href="https://pypi.org/project/embedchain/">
    <img src="https://img.shields.io/pypi/v/embedchain" alt="PyPI">
  </a>
  <a href="https://pepy.tech/project/embedchain">
    <img src="https://static.pepy.tech/badge/embedchain" alt="Downloads">
  </a>
  <a href="https://embedchain.ai/slack">
    <img src="https://img.shields.io/badge/slack-embedchain-brightgreen.svg?logo=slack" alt="Slack">
  </a>
  <a href="https://embedchain.ai/discord">
    <img src="https://dcbadge.vercel.app/api/server/6PzXDgEjG5?style=flat" alt="Discord">
  </a>
  <a href="https://twitter.com/embedchain">
    <img src="https://img.shields.io/twitter/follow/embedchain" alt="Twitter">
  </a>
  <a href="https://colab.research.google.com/drive/138lMWhENGeEu7Q1-6lNbNTHGLZXBBz_B?usp=sharing">
    <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open in Colab">
  </a>
  <a href="https://codecov.io/gh/embedchain/embedchain">
    <img src="https://codecov.io/gh/embedchain/embedchain/graph/badge.svg?token=EMRRHZXW1Q" alt="codecov">
  </a>
</p>

<hr />

## 什麼是 Embedchain？

Embedchain 是一個用於個人化 LLM 回應的開源框架。它讓建立與部署個人化 AI 應用程式變得非常簡單。Embedchain 的核心遵循「常規但可配置」(*Conventional but Configurable*) 的設計原則，同時為軟體工程師與機器學習工程師提供服務。

Embedchain 簡化了個人化 LLM 應用程式的建立過程，為管理各種類型的非結構化數據提供無縫流程。它能高效地將數據分割為易於管理的區塊 (Chunks)、生成相關的嵌入向量，並將其儲存在向量資料庫中以實現優化檢索。透過一套多樣化的 API，使用者可以提取上下文資訊、尋找精確答案或進行互動式聊天對話，這一切都是根據他們自己的數據量身定制的。

## 🔧 快速安裝

### Python API

```bash
pip install embedchain
```

## ✨ 線上演示 (Live Demo)

查看我們使用 Embedchain 建立的 [與 PDF 聊天](https://embedchain.ai/demo/chat-pdf) 線上演示。您可以在[此處](https://github.com/mem0ai/mem0/tree/main/embedchain/examples/chat-pdf)找到原始碼。

## 🔍 用法

<!-- Demo GIF or Image -->
<p align="center">
  <img src="docs/images/cover.gif" width="900px" alt="Embedchain Demo">
</p>

例如，您可以使用以下程式碼建立一個 Elon Musk 機器人：

```python
import os
from embedchain import App

# 建立機器人實例
os.environ["OPENAI_API_KEY"] = "<您的-API-金鑰>"
app = App()

# 嵌入線上資源
app.add("https://en.wikipedia.org/wiki/Elon_Musk")
app.add("https://www.forbes.com/profile/elon-musk")

# 查詢應用程式
app.query("Elon Musk 經營多少家公司？請列出名稱。")
# 答案：Elon Musk 目前經營多家公司。根據我的了解，他是 SpaceX 的執行長兼首席設計師、Tesla, Inc. 的執行長兼產品架構師、Neuralink 的執行長兼創辦人，以及 The Boring Company 的執行長兼創辦人。不過請注意，這些資訊可能會隨時間變動，因此最好查閱最新更新以進行驗證。
```

您也可以在瀏覽器中透過 Google Colab 嘗試：

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/17ON1LPonnXAtLaZEebnOktstB_1cJJmh?usp=sharing)

## 📖 說明文件
我們提供全面的指南與 API 文件，協助您充分利用 Embedchain：

- [簡介](https://docs.embedchain.ai/get-started/introduction#what-is-embedchain)
- [開始使用](https://docs.embedchain.ai/get-started/quickstart)
- [範例](https://docs.embedchain.ai/examples)
- [支援的數據類型](https://docs.embedchain.ai/components/data-sources/overview)

## 🔗 加入社群

* 加入我們的 [Slack 社群](https://embedchain.ai/slack) 或 [Discord 社群](https://embedchain.ai/discord)，與其他開發者交流。

* 參與 [GitHub 討論區](https://github.com/embedchain/embedchain/discussions)，提出問題或分享您的經驗。

## 🤝 預約 1 對 1 會談

向創辦人預約 [1 對 1 會談](https://cal.com/taranjeetio/ec)，討論任何問題、提供回饋或探索我們如何為您改進 Embedchain。

## 🌐 貢獻

歡迎任何形式的貢獻！請查看儲存庫中的 Issue，並隨時發起 Pull Request。
更多資訊請參閱 [貢獻指南](CONTRIBUTING_zh_TW.md)。

更多參考資料請查閱 [開發指南](https://docs.embedchain.ai/contribution/dev) 與 [文件指南](https://docs.embedchain.ai/contribution/docs)。

<a href="https://github.com/embedchain/embedchain/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=embedchain/embedchain" />
</a>

## 匿名遙測 (Anonymous Telemetry)

我們收集匿名使用數據以提升套件品質與使用者體驗。這包括功能使用頻率與系統資訊等數據，但絕不包含個人細節。這些數據協助我們優先進行改進並確保相容性。如果您希望退出，請設定環境變數 `EC_TELEMETRY=false`。我們重視數據安全，且不會對外分享這些數據。

## 引用 (Citation)

如果您使用了此儲存庫，請考慮引用：

```
@misc{embedchain,
  author = {Taranjeet Singh, Deshraj Yadav},
  title = {Embedchain: The Open Source RAG Framework},
  year = {2023},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/embedchain/embedchain}},
}
```
