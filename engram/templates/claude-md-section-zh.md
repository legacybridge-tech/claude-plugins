<!-- ENGRAM:START -->
<!-- Engram 記憶系統 v{VERSION} | 預設: {PRESET} | 請勿手動編輯標記之間的內容 — 重新執行 memory-init 即可更新 -->

## Engram 記憶系統

這個專案啟用了跨對話的持久記憶，儲存在三個 markdown 檔案：

- **`{PREFS_FILE}`** — 使用者/專案偏好。下方已用 `@` 自動載入到 context，不需要手動讀。
- **`{CONVOS_FILE}`** — 對話摘要歷史，新的在最前。話題相關時用 Grep 搜尋。
- **`{LONGTERM_FILE}`** — 永久記錄（決策、里程碑、關鍵事件），只增不刪,話題相關時用 Grep 搜尋。

### 工作方式

- 話題可能涉及使用者過去的偏好、決策或歷史對話時，用 `memory-recall` skill 搜尋後再回覆。
- 發現值得長期保留的新資訊（偏好、決策、里程碑），或對話到達自然總結點時，用 `memory-remember` skill 寫入對應檔案。

{SEARCH_MODE_INSTRUCTION}

## 記憶偏好
@{PREFS_FILE}
<!-- ENGRAM:END -->
