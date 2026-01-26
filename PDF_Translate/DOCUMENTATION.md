# 📚 PDF2CHT 文檔指南

**版本**: v2.1.0
**最後更新**: 2026-01-26
**狀態**: ✅ 完整

---

## 🎯 快速導航

我想...| 看這個文檔 | ⏱️ 需時
---|---|---
🚀 快速開始 | [QUICKSTART.md](./QUICKSTART.md) | 2 分鐘
📖 完整說明 | [README.md](./README.md) | 15 分鐘
⚙️ 進階用法 | [USAGE.md](./USAGE.md) | 10 分鐘
🔧 工作流程 | [WORKFLOW.md](./WORKFLOW.md) | 8 分鐘
💾 項目記憶 | [CLAUDE.md](./CLAUDE.md) | 10 分鐘
🐛 故障排除 | [ERROR_DIAGNOSTIC_GUIDE](./docs/ERROR_DIAGNOSTIC_GUIDE.md) | 5 分鐘
📐 架構圖表 | [架構圖表（HTML）](./docs/architecture-diagrams.html) | 10 分鐘

---

## 📂 文檔結構一覽

```
PDF2CHT/
│
├── 📋 核心文檔（推薦閱讀）
│   ├── QUICKSTART.md           ← 新手必讀：2 分鐘快速上手
│   ├── README.md               ← 完整項目說明和功能介紹
│   ├── CLAUDE.md               ← 項目記憶：架構、決策、v2.1.0 更新
│   ├── USAGE.md                ← 進階選項和配置指南
│   ├── WORKFLOW.md             ← 工作流程文檔
│   └── DOCUMENTATION.md         ← 本文件（文檔導航指南）
│
├── 📐 視覺化和技術文檔
│   └── docs/
│       ├── architecture-diagrams.html  ← ⭐ 9 個互動式架構圖表
│       ├── ERROR_DIAGNOSTIC_GUIDE.md   ← 11+ 個常見錯誤和解決方案
│       └── archive/                    ← 歸檔文檔（見下方）
│
├── 📦 過程文檔歸檔
│   └── docs/archive/
│       ├── PROCESS_DOCS_INDEX.md       ← 歸檔索引和導航
│       ├── process-docs/               ← 開發過程文檔
│       │   ├── LEARNING_SESSION.md
│       │   ├── BACKUP_MANIFEST.md
│       │   ├── IMPLEMENTATION_SUMMARY.md
│       │   └── ...（其他技術筆記）
│       └── references/                 ← 參考資源
│
├── 🔧 操作腳本
│   ├── run1.sh                 ← API 連線測試
│   ├── run2.sh                 ← PDF 轉圖檔
│   ├── run3.sh                 ← 圖檔翻譯（批量）
│   ├── run3-single.sh           ← 單張圖檔翻譯
│   └── run4.sh                 ← 合併回 PDF
│
└── 💻 原始碼和配置
    ├── src/pdf2cht/            ← Python 源代碼
    ├── tests/                  ← 測試套件
    ├── conductor/              ← 工程框架和提示詞
    └── pyproject.toml          ← 項目配置
```

---

## 🚀 按照您的角色選擇文檔

### 👤 角色 1: 新手用戶

**目標**: 快速了解和使用 PDF2CHT

**推薦步驟**:
```
1. 閱讀 QUICKSTART.md          (2 分鐘)
   ↓
2. 執行 run1.sh 測試 API      (1 分鐘)
   ↓
3. 嘗試 run2.sh + run3.sh      (5 分鐘)
   ↓
4. 查看 README.md 了解詳情    (15 分鐘)
```

**常用文檔**:
- ✅ QUICKSTART.md
- ✅ README.md
- ✅ 架構圖表
- 🟡 ERROR_DIAGNOSTIC_GUIDE.md（遇到問題時）

---

### 🏗️ 角色 2: 開發者/貢獻者

**目標**: 理解架構，參與開發或改進

**推薦步驟**:
```
1. 閱讀 CLAUDE.md              (10 分鐘)
   ↓
2. 查看架構圖表                (10 分鐘)
   ↓
3. 研究 src/pdf2cht/ 源代碼    (15 分鐘)
   ↓
4. 閱讀 LEARNING_SESSION.md    (15 分鐘)
   ↓
5. 執行 make test              (5 分鐘)
```

**常用文檔**:
- ✅ CLAUDE.md
- ✅ 架構圖表
- ✅ WORKFLOW.md
- 🟡 docs/archive/LEARNING_SESSION.md
- 🟡 src/pdf2cht/ 源代碼註釋

---

### 🔧 角色 3: 系統管理員/運維

**目標**: 部署、配置、監控和維護

**推薦步驟**:
```
1. 閱讀 QUICKSTART.md          (2 分鐘)
   ↓
2. 閱讀 USAGE.md (進階配置)   (10 分鐘)
   ↓
3. 查看 WORKFLOW.md            (8 分鐘)
   ↓
4. 保存 ERROR_DIAGNOSTIC_GUIDE.md 備用 (作為快速參考)
```

**常用文檔**:
- ✅ USAGE.md
- ✅ ERROR_DIAGNOSTIC_GUIDE.md
- ✅ WORKFLOW.md
- 🟡 CLAUDE.md（了解決策邏輯）

---

### 🎓 角色 4: 學習者/研究者

**目標**: 深入理解系統設計和實現細節

**推薦步驟**:
```
1. 閱讀 CLAUDE.md              (10 分鐘)
   ↓
2. 查看架構圖表                (10 分鐘)
   ↓
3. 研究 LEARNING_SESSION.md    (15 分鐘)
   ↓
4. 閱讀 IMPLEMENTATION_SUMMARY.md (15 分鐘)
   ↓
5. 深潛源代碼                  (根據需要)
```

**常用文檔**:
- ✅ CLAUDE.md
- ✅ 架構圖表
- ✅ docs/archive/LEARNING_SESSION.md
- ✅ docs/archive/IMPLEMENTATION_SUMMARY.md
- 🟡 src/pdf2cht/ 源代碼

---

## 📋 文檔詳細說明

### 核心文檔（必讀）

#### QUICKSTART.md
- **用途**: 2 分鐘快速上手
- **內容**: 安裝、配置、執行三步驟
- **適合**: 所有新用戶

#### README.md
- **用途**: 完整項目文檔
- **內容**: 功能特性、系統架構、提示詞詳解、工作流程
- **適合**: 需要全面了解的用戶

#### CLAUDE.md
- **用途**: 項目記憶和決策框架
- **內容**: v2.1.0 新功能、技術選型、核心決策、工作流範例
- **適合**: 開發者和研究者

#### USAGE.md
- **用途**: 進階使用和配置
- **內容**: 高級選項、環境變數、自訂設定、故障排除
- **適合**: 需要自訂配置的進階用戶

#### WORKFLOW.md
- **用途**: 工作流程文檔
- **內容**: 三步驟工作流、完整流程範例、常見場景
- **適合**: 想了解工作流程的用戶

---

### 視覺化文檔

#### 架構圖表（architecture-diagrams.html）
- **用途**: 互動式架構和流程圖
- **內容**: 9 個圖表涵蓋完整系統
- **適合**: 視覺化學習者

#### ERROR_DIAGNOSTIC_GUIDE.md
- **用途**: 故障診斷和解決方案
- **內容**: 11+ 個常見錯誤、根本原因、解決步驟
- **適合**: 遇到問題需要快速解決的用戶

---

### 歸檔文檔（參考用）

#### docs/archive/PROCESS_DOCS_INDEX.md
- **用途**: 過程文檔的索引和導航
- **內容**: 各過程文檔的說明、使用場景、何時參考
- **適合**: 想要瞭解開發歷程的用戶

#### docs/archive/process-docs/LEARNING_SESSION.md
- **用途**: 開發歷程和學習記錄
- **內容**: 各階段決策、技術亮點、挑戰與解決方案
- **適合**: 研究者和貢獻者

---

## 🔍 常見問題 → 相應文檔

| 常見問題 | 推薦文檔 |
|---------|---------|
| 怎樣開始使用？ | QUICKSTART.md |
| 所有功能是什麼？ | README.md |
| 如何自訂配置？ | USAGE.md |
| 圖表怎樣工作？ | 架構圖表 |
| 遇到錯誤怎辦？ | ERROR_DIAGNOSTIC_GUIDE.md |
| 為什麼這樣設計？ | CLAUDE.md |
| 怎樣進行翻譯？ | WORKFLOW.md |
| 想研究代碼？ | CLAUDE.md + 源代碼 |

---

## 💡 文檔使用建議

✅ **第一次使用？**
→ 從 QUICKSTART.md 開始，然後看 README.md

✅ **想深入了解？**
→ 閱讀 CLAUDE.md 和架構圖表

✅ **遇到問題？**
→ 查看 ERROR_DIAGNOSTIC_GUIDE.md

✅ **要參與開發？**
→ 閱讀 CLAUDE.md 和 LEARNING_SESSION.md

✅ **需要進階配置？**
→ 參考 USAGE.md

---

## 🎯 文檔維護信息

- **最後更新**: 2026-01-26
- **版本**: v2.1.0
- **維護者**: Claude Haiku 4.5
- **狀態**: ✅ 完整且最新

---

## 📞 需要幫助？

1. **快速問題？** → 查看 ERROR_DIAGNOSTIC_GUIDE.md
2. **概念不清？** → 參考架構圖表
3. **使用問題？** → 閱讀 USAGE.md
4. **開發相關？** → 查看 CLAUDE.md

---

**祝您使用愉快！** 🚀

