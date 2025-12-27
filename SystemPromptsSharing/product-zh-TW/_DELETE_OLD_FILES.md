# 舊檔案標記清單

> **這些檔案已過時，請手動刪除**

---

## 📁 需要刪除的檔案（舊版三模式設計）

1. `c:\Users\User\Desktop\systemPmt\product-zh-TW\prompts\query-only.md`
2. `c:\Users\User\Desktop\systemPmt\product-zh-TW\prompts\query-action.md`
3. `c:\Users\User\Desktop\systemPmt\product-zh-TW\prompts\full-solo.md`

---

## 原因

- 已改為**統一安全模式**（Query/Action/Solo 三模式整合）
- 新版系統提示詞位於：`.github/copilot-instructions.md`
- 舊版分離式設計已廢棄

---

## 如何刪除

### 方法 1：手動刪除
1. 打開檔案總管
2. 導航到 `product-zh-TW\prompts\` 資料夾
3. 選擇以上三個檔案
4. 按 Delete 鍵

### 方法 2：使用 PowerShell
```powershell
cd c:\Users\User\Desktop\systemPmt\product-zh-TW\prompts
Remove-Item query-only.md
Remove-Item query-action.md
Remove-Item full-solo.md
```

---

**刪除後可以刪除此檔案**：`_DELETE_OLD_FILES.md`
