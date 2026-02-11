# 貢獻給 mem0

讓我們讓貢獻變得簡單、具協作性且充滿樂趣。

## 透過 PR 提交您的貢獻

若要進行貢獻，請遵循以下步驟：

1. Fork 並複製 (clone) 此儲存庫。
2. 在您的 Fork 中建立專門的功能分支（例如 `feature/f1`）進行修改。
3. 如果您修改了程式碼（新功能或錯誤修復），請為其新增測試。
4. 包含適當的文件 / Docstring 以及執行該功能的範例。
5. 確保所有測試皆已通過。
6. 提交 Pull Request (PR)。

有關 Pull Request 的更多詳細資訊，請閱讀 [GitHub 指南](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request)。


### 📦 開發環境

我們使用 `hatch` 來管理開發環境。設定步驟如下：

```bash
# 為特定的 Python 版本啟動環境：
hatch shell dev_py_3_9   # Python 3.9
hatch shell dev_py_3_10  # Python 3.10  
hatch shell dev_py_3_11  # Python 3.11
hatch shell dev_py_3_12  # Python 3.12

# 環境將自動安裝所有開發依賴項目
# 在啟動的 Shell 中執行測試：
make test
```

### 📌 Pre-commit

為了確保符合我們的標準，請在開始貢獻前安裝 pre-commit。

```bash
pre-commit install
```

### 🧪 測試

我們使用 `pytest` 來測試不同 Python 版本下的程式碼。您可以使用以下指令執行測試：

```bash
# 使用預設 Python 版本執行測試
make test

# 測試特定的 Python 版本：
make test-py-3.9   # Python 3.9 環境
make test-py-3.10  # Python 3.10 環境
make test-py-3.11  # Python 3.11 環境
make test-py-3.12  # Python 3.12 環境

# 當使用 hatch shell 時，使用以下指令執行測試：
make test  # 在使用 hatch shell test_XX 啟動 shell 之後
```

在提交 Pull Request 之前，請確保所有測試在所有支援的 Python 版本中皆已通過。

我們期待您的 Pull Request，並迫不及待想看到您的貢獻！
