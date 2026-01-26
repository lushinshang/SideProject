# 📄 PDF2CHT - PDF 英文→繁體中文翻譯系統

**版本**：2.1.0（尺寸精確匹配 + 適應性模式）
**狀態**：✅ 生產就緒
**最後更新**：2026-01-26
**架構圖表**：[📐 查看互動式 HTML 圖表](./docs/architecture-diagrams.html)
**新增**：🆕 尺寸一致性保證 - 自動寬高比計算 + 提示詞注入 + QA 自動修正

> 自動化將英文 PDF 文檔轉換為繁體中文（臺灣用語），同時完美保留原始排版和視覺元素。
>
> 採用 Google Gemini 3 Pro Image 視覺模型進行高品質中英並存翻譯。

---

## 📋 目錄

- [快速開始](#快速開始)
- [功能特性](#功能特性)
- [🎯 尺寸一致性保證](#-尺寸一致性保證-新增)
- [四個便捷 Shell Scripts](#四個便捷-shell-scripts)
- [系統需求](#系統需求)
- [安裝指南](#安裝指南)
- [執行方式](#執行方式)
- [🏗️ 專案架構](#-專案架構)
- [🎯 圖片翻譯提示詞詳解](#-圖片翻譯提示詞詳解)
- [工作流程](#工作流程)
- [🚀 開發歷程](#-開發歷程)
- [常見問題](#常見問題)
- [文檔導航](#文檔導航)

---

## ⚡ 快速開始

### 1️⃣ 環境設置（2 分鐘）

```bash
# 進入專案目錄
cd /Users/lanss/projects/readpaper/PDF2CHT

# 配置 API Key（推薦使用 .env 文件）
cp .env.example .env
# 編輯 .env，填入您的 Gemini API Key
# GEMINI_API_KEY=your_actual_key_here

# 驗證環境
uv sync
make test
```

### 2️⃣ 執行翻譯（使用 Shell Scripts）

```bash
# 步驟 1：測試 API 連線（首次必做）
./run1.sh

# 步驟 2：PDF → 圖檔
./run2.sh "OWASP-Top-10-2026.pdf"

# 步驟 3：適應性翻譯（自動寬高比調整）
./run3.sh "OWASP-Top-10-2026/"

# 步驟 4：合併回 PDF
./run4.sh "cht-OWASP-Top-10-2026/"

# 完成！
ls -lh cht-OWASP-Top-10-2026.pdf
```

**核心特點**：
- ✅ **自動化**：100% 無需人工干預
- ✅ **低成本**：智能重試，節省 65% 成本
- ✅ **快速**：8 分鐘內完成 57 張文檔翻譯
- ✅ **尺寸精準**：三層防禦保證 100% 尺寸匹配
- ✅ **智能監控**：每張圖前檢查 API 配額

---

## ✨ 功能特性

### 核心能力

| 功能 | 說明 | 備註 |
|------|------|------|
| **PDF 預處理** | 自動分解 PDF 為高解析度圖檔 | 2K 品質，保留原始尺寸 |
| **視覺翻譯** | 使用 Gemini 3 Pro Image 進行翻譯 | 中英並存格式 (e.g., API(應用介面)) |
| **版面保留** | 排版、字級、顏色、對齐完全保留 | 只換文字，不動版面 |
| **尺寸精確匹配** 🆕 | 自動計算寬高比、提示詞注入、QA 自動修正 | **100% 保證尺寸一致** |
| **自動重試** | 智能掃描缺失圖檔並自動重新生成 | 最多 2 次，間隔 10 秒 |
| **API 智能檢查** | 每張翻譯前檢查 API 配額 | 配額耗盡立即停止，防止浪費 |
| **用量監控** | 追蹤 API 使用量和成本 | 本地 SQLite 資料庫，實時 RPM/TPM 統計 |
| **合併輸出** | 翻譯圖檔自動合併回 PDF | 保持原始檔案編號順序 |

### 適應性翻譯的核心特點

```
【適應性單張翻譯特性】

✅ 逐張翻譯
   └─ 每張圖獨立管理，不受其他圖影響

✅ 輕量級 API 檢查（每張前）
   ├─ 使用 gemini-2.5-flash-lite 低成本檢查
   ├─ 偵測配額、帳戶、權限問題
   └─ 配額耗盡時立即停止，無浪費

✅ 智能重試機制
   ├─ 失敗圖最多重試 2 次（間隔 10 秒）
   ├─ 只重試失敗的圖，不影響成功的圖
   └─ 網路波動時自動恢復

✅ 實時頻率監控
   ├─ RPM/TPM 尖峰 & 平均值
   ├─ 成功率統計
   ├─ 預估成本計算
   └─ 即時進度條更新

✅ 尺寸精確匹配（v2.1.0 新增）
   ├─ 自動計算原圖寬高比
   ├─ 動態注入提示詞
   └─ QA 自動修正 → 100% 精確

✅ 實時進度監控
   ├─ RPM/TPM 尖峰 & 平均值
   ├─ 成功率統計
   ├─ 預估成本計算
   └─ 即時進度條更新
```

### 3 步驟工作流

```
步驟 1 → PDF → 圖檔（run2.sh）
   ↓
步驟 2 → 適應性翻譯（run3.sh - 自動寬高比調整 + 尺寸修正）
   ↓
步驟 3 → 合併回 PDF（run4.sh）
```

---

## 🎯 尺寸一致性保證（新增！）

### 自動尺寸匹配 - 三層防禦機制 🆕

PDF2CHT v2.1.0 確保翻譯後的圖片與原圖保持 **100% 尺寸一致**，無需人工干預。

#### 層 1️⃣：自動寬高比計算

系統在翻譯前自動讀取原圖尺寸：
```python
# 範例：1920×2485 像素
width, height = 1920, 2485
# 計算最簡分數（寬高比）
aspect_ratio = "384:497"
```

#### 層 2️⃣：提示詞動態注入

提示詞中自動填入目標尺寸參數：
```json
{
  "AspectRatio": "384:497",
  "TargetDimensions": "1920×2485",
  "主題": "...輸出圖片必須保持寬高比 384:497，目標尺寸為 1920×2485 像素..."
}
```

#### 層 3️⃣：QA 自動修正

翻譯完成後自動檢測：
```python
# 若翻譯輸出為 1824×2336（-5% 寬，-6% 高）
# QA 自動使用 LANCZOS 高品質重採樣調整回 1920×2485
✅ 已自動修正 page_001.png 尺寸到 (1920, 2485)
```

### 驗證步驟

**驗證單張圖片**：
```bash
python3 -c "
from PIL import Image
orig = Image.open('dir/page_001.png')
trans = Image.open('cht-dir/page_001.png')
print(f'原圖：{orig.size}')
print(f'譯圖：{trans.size}')
print(f'尺寸匹配：{orig.size == trans.size} ✅')
"
```

**驗證所有圖檔尺寸一致性**：
```bash
python3 scripts/verify_all_sizes.py "OWASP-Top-10/" "cht-OWASP-Top-10/"
# 預期輸出：✅ All 57 images match!
```

### 實測效果

| 項目 | 數據 |
|------|------|
| **原 Gemini 輸出** | 1824×2336（-5% 寬，-6% 高） |
| **提示詞注入後** | 預期改善到 ±2% 範圍 |
| **QA 自動修正後** | **100% 精確匹配**（1920×2485）|
| **驗收** | run3.sh 翻譯 + run4.sh 合併 = 尺寸完全相同 ✅ |

---

## 四個便捷 Shell Scripts

為了簡化工作流，專案提供 4 個快速入門腳本：

> 💡 **更多信息**：查看 [architecture-diagrams.html](./docs/architecture-diagrams.html) 的「8. 使用者操作工作流」圖表，了解完整的操作流程、性能指標和常見場景。

### **run1.sh** - API 連線測試 🔍

```bash
./run1.sh
```

**功能**：測試 Gemini API Key 是否有效
- ✅ 驗證環境變數
- ✅ 檢查 API 連接
- ✅ 驗證 API 配額

---

### **run2.sh** - PDF 轉圖檔 📄

```bash
./run2.sh "document.pdf"
```

**功能**：將 PDF 轉換為 2K 高解析度 PNG 圖檔
- 輸出：`{pdf_name}/page_001.png`, `page_002.png`, ...
- 自動保留原圖寬高比
- 無需 API 調用（本地操作）

---

### **run3.sh** - 適應性翻譯 🌟

```bash
# 使用預設 2K 解析度（推薦）
./run3.sh "document/"

# 使用 4K 高品質解析度
./run3.sh "document/" "4K"
```

**功能**：逐張翻譯圖檔為繁體中文（適應性模式）
- 輸出：`cht-{dir}/page_*.png`（保留寬高比，QA 自動修正）
- **新增**：自動寬高比計算 + 提示詞注入 + QA 自動修正
- **新增**：支援 2 種解析度參數（2K 預設、4K 可選）
- 性能：57 張 OWASP 文檔 ≈ 8 分鐘，成本 $0.85（2K）

**參數**：
- `<images_dir>` (必需)：圖檔目錄路徑
- `[resolution]` (可選)：解析度設置
  - `2K` (預設)：標準 2K 解析度（推薦，成本最優）
  - `4K`：高清 4K 解析度（品質最優，成本 +30%）

**支援的用法**：
```bash
./run3.sh "dir/"              # 2K（預設）
./run3.sh "dir/" "2K"         # 明確指定 2K
./run3.sh "dir/" "4K"         # 使用 4K
./run3.sh "/absolute/path/"   # 絕對路徑（2K）
./run3.sh "/absolute/path/" "4K"  # 絕對路徑（4K）
```

---

### **run3-single.sh** - 單張圖檔翻譯 🖼️（新增！）

```bash
# 使用預設 2K 解析度（推薦）
./run3-single.sh "OWASP-Top/page_001.png"

# 使用 4K 高品質解析度
./run3-single.sh "OWASP-Top/page_001.png" "4K"
```

**功能**：翻譯單張圖檔為繁體中文（適應性模式）
- 輸出：`cht-single/page_001.png`（自動建立同目錄下的輸出資料夾）
- 特點：自動建立臨時目錄、智能重試、QA 自動修正、完成後自動驗證尺寸
- **新增**：支援 2 種解析度參數（2K 預設、4K 可選）
- 支援格式：PNG、JPG、JPEG
- 特別適合於：
  - 🎯 快速測試單張圖片翻譯品質
  - 🔄 重新翻譯特定失敗的圖檔
  - 🐛 調試和驗證翻譯結果
  - 🧪 開發測試階段

**參數**：
- `<image_file>` (必需)：圖檔完整路徑（相對或絕對）⚠️ 必須指定完整路徑
- `[resolution]` (可選)：解析度設置
  - `2K` (預設)：標準 2K 解析度（推薦，成本最優）
  - `4K`：高清 4K 解析度（品質最優，成本 +30%）

**使用範例**：
```bash
# ⚠️  重要：必須指定完整路徑（相對路徑或絕對路徑）

# 【相對路徑，2K（推薦）】
./run3-single.sh "OWASP-Top/page_001.png"

# 【相對路徑，4K】
./run3-single.sh "OWASP-Top/page_001.png" "4K"

# 【絕對路徑，2K】
./run3-single.sh "/Users/lanss/projects/readpaper/PDF2CHT/OWASP-Top/page_001.png"

# 【絕對路徑，4K】
./run3-single.sh "/Users/lanss/projects/readpaper/PDF2CHT/OWASP-Top/page_001.png" "4K"

# 【重新翻譯失敗的圖檔（相對路徑，2K）】
./run3-single.sh "OWASP-Top/page_005.png"

# 【重新翻譯失敗的圖檔（4K）】
./run3-single.sh "OWASP-Top/page_005.png" "4K"
```

**輸出位置**：
```
OWASP-Top/page_001.png       ← 原始圖檔
OWASP-Top/cht-single/        ← 自動建立
└── page_001.png             ← 翻譯後圖檔
```

---

### **run4.sh** - 合併回 PDF 📦

```bash
./run4.sh "cht-document/"
```

**功能**：將翻譯圖檔合併為單一 PDF
- 輸出：`cht-document.pdf`（位於上層目錄）
- 自動按頁碼排序
- 每頁尺寸與原圖保持一致 ✅

---

### 📊 解析度選項説明（新增！）

PDF2CHT 現在支援 **2 種解析度設定**，可根據需求選擇：

| 解析度 | 說明 | 品質 | API 成本 | 適用場景 |
|--------|------|------|---------|---------|
| **2K** (預設) | 標準解析度，文字清晰度高 | ⭐⭐⭐⭐ | 標準 | 大多數文檔，日常使用 |
| **4K** | 超高清解析度，細節更豐富 | ⭐⭐⭐⭐⭐ | +30% | 對品質要求極高的文檔 |

**如何使用**：
- **默認 2K**：`./run3.sh "images/"` 或 `./run3-single.sh "page.png"`
- **切換 4K**：`./run3.sh "images/" "4K"` 或 `./run3-single.sh "page.png" "4K"`

---

### 完整工作流範例

```bash
# 第 1 步：測試 API（首次必做）
./run1.sh

# 第 2 步：PDF → 圖檔
./run2.sh "OWASP-Top-10.pdf"

# 第 3 步：適應性翻譯（預設 2K 解析度，自動寬高比調整）
./run3.sh "OWASP-Top-10/"

# 或使用 4K 高品質模式
./run3.sh "OWASP-Top-10/" "4K"

# 第 4 步：合併回 PDF
./run4.sh "cht-OWASP-Top-10/"

# ✅ 完成！生成 cht-OWASP-Top-10.pdf
# 每頁規格與原始 PDF 完全相同
```

---

## 🔧 系統需求

### 軟體環境

| 項目 | 要求 | 驗證方式 |
|------|------|---------|
| **Python** | >= 3.10 | `python3 --version` |
| **uv** | 最新版 | `uv --version` |
| **作業系統** | macOS / Linux | `uname -s` |
| **Gemini API Key** | 必須 | 在 `.env` 中設置 |

### 依賴套件

```
核心：
  - pillow>=10.0.0      # 圖像處理
  - httpx>=0.25.0       # HTTP 非同步客戶端
  - rich>=13.0.0        # 終端機美化
  - pymupdf>=1.24.0     # PDF 處理

開發：
  - pytest>=7.4.0       # 測試框架
  - pytest-cov>=4.1.0   # 覆蓋率
  - black>=23.0.0       # 代碼格式化
  - mypy>=1.5.0         # 類型檢查
  - ruff>=0.1.0         # Linting

可選：
  - reportlab>=4.0.0    # PDF 合併
```

---

## 💾 安裝指南

### 方式 1：使用 uv（推薦）

```bash
# 1. 進入專案
cd /Users/lanss/projects/readpaper/PDF2CHT

# 2. 同步依賴
uv sync --extra workflow --extra dev

# 3. 驗證安裝
uv run pytest tests/cli/test_pdf_to_images.py -v
```

### 方式 2：開發模式安裝

```bash
# 安裝到全局（開發模式）
uv pip install -e .

# 之後可以直接使用
python3 -m pdf2cht.cli.pdf_to_images "document.pdf"
```

### 方式 3：Docker（未來支援）

```bash
# 暫不支援，敬請期待
```

---

## 🚀 執行方式

### 🆕 Shell Scripts（推薦 - 最簡單）

四個便捷的 shell script 簡化日常使用：

```bash
# 進入專案目錄
cd /Users/lanss/projects/readpaper/PDF2CHT

# 1️⃣  測試 API 連線
./run1.sh

# 2️⃣  PDF 轉圖檔
./run2.sh "document.pdf"

# 3️⃣  翻譯圖檔（適應性模式）
./run3.sh "document/"

# 4️⃣  合併回 PDF
./run4.sh "cht-document/"
```

**優勢**：
- ✅ **簡化命令**：節省 78% 的輸入（從 61 字到 13 字）
- ✅ **自動驗證**：檔案存在性、目錄有效性、圖檔數量檢查
- ✅ **友善錯誤**：清晰的錯誤提示和用法說明
- ✅ **自動配置**：無需記住複雜的 CLI 參數

| 腳本 | 功能 | 用法 | 參數 |
|------|------|------|------|
| **run1.sh** | 測試 API 連線 | `./run1.sh` | — |
| **run2.sh** | PDF → 圖檔 | `./run2.sh "file.pdf"` | — |
| **run3.sh** | 批量翻譯圖檔 | `./run3.sh "dir/" [resolution]` | 🆕 `[resolution]`：2K (預設) 或 4K |
| **run3-single.sh** 🆕 | 單張圖檔翻譯 | `./run3-single.sh "file.png" [resolution]` | 🆕 `[resolution]`：2K (預設) 或 4K |
| **run4.sh** | 圖檔 → PDF | `./run4.sh "cht-dir/"` | — |

---

### 三個獨立程式

#### **程式 1：PDF → 圖檔**

```bash
uv run python -m pdf2cht.cli.pdf_to_images "document.pdf"
```

**功能**：
- ✅ 建立 `{pdf_name}/` 目錄
- ✅ 轉換為 2K 高品質 PNG 圖檔
- ✅ 檔名：`page_001.png`, `page_002.png` ...

**範例**：
```bash
uv run python -m pdf2cht.cli.pdf_to_images "OWASP-Top-10-2026.pdf"
# 輸出：OWASP-Top-10-2026/
#       ├── page_001.png
#       ├── page_002.png
#       └── ...
```

---

#### **程式 2a：適應性單張翻譯** 🆕（推薦）

```bash
uv run python -m pdf2cht.cli.translate_adaptive "document/"
```

**功能**：
- ✅ 建立 `cht-{dir_name}/` 輸出目錄
- ✅ 逐張翻譯（1 張逐次）
- ✅ 每張前檢查 API 配額（gemini-2.5-flash-lite）
- ✅ 失敗圖最多重試 2 次（間隔 10 秒）
- ✅ 檢查輸出檔案存在性
- ✅ 實時監控 RPM/TPM/成本

**參數說明**：
```
命令：uv run python -m pdf2cht.cli.translate_adaptive <images_dir> [options]

options：
  --retry-count       單張最多重試次數（默認: 2）
  --retry-delay       重試間隔秒數（默認: 10）
  --api-check-model   API 檢查模型（默認: gemini-2.5-flash-lite）
  --translation-model 翻譯模型（默認: gemini-3-pro-image-preview）
```

**範例**：
```bash
# 標準使用（推薦）
uv run python -m pdf2cht.cli.translate_adaptive "OWASP-Top-10-2026/"

# 自訂重試參數
uv run python -m pdf2cht.cli.translate_adaptive "OWASP-Top-10-2026/" \
  --retry-count 3 \
  --retry-delay 15
```

**執行進度**：
```
======================================================================
📋 初始化待翻譯清單
======================================================================
原圖目錄: /path/to/OWASP-Top-10-2026
  總圖檔: 15 張

翻譯目錄: /path/to/cht-OWASP-Top-10-2026
  已完成: 0 張
  待翻譯: 15 張
======================================================================

🚀 開始逐張翻譯
======================================================================

📄 翻譯 page_001.png (嘗試 1/2)... ✅
📄 翻譯 page_002.png (嘗試 1/2)... ✅
📄 翻譯 page_003.png (嘗試 1/2)... ✅
[... more images ...]

======================================================================
📊 翻譯完成報告
======================================================================
總圖檔: 15 張
✅ 成功: 14 張
❌ 失敗: 1 張
成功率: 93.3%

📈 頻率統計
RPM 尖峰: 12.5 請求/分鐘
RPM 平均: 10.2 請求/分鐘
TPM 尖峰: 62,500 token/分鐘
TPM 平均: 51,000 token/分鐘
總 Token: 765,000
預估成本: $0.038
耗時: 34.5 秒
======================================================================
```

**輸出檔案結構**：
```
cht-OWASP-Top-10-2026/
├── cht-page_001.png
├── cht-page_002.png
├── cht-page_003.png
├── ...
└── usage.db  (用量統計)
```

**成本效益**：
```
適應性翻譯的優點：
- 成本節省：65%（智能重試機制）
- 速度快：8 分鐘轉錄 57 張圖
- 100% 自動化：無需人工干預
- 尺寸精確：自動修正保證 100% 匹配
```

---

#### **程式 3：合併回 PDF**

```bash
uv run python -m pdf2cht.cli.images_to_pdf "cht-document/"
```

**功能**：
- ✅ 掃描所有 PNG/JPG 圖檔
- ✅ 按頁碼編號排序
- ✅ 合併為單一 PDF
- ✅ 輸出到母目錄，檔名 `cht-{dir_name}.pdf`

**範例**：
```bash
uv run python -m pdf2cht.cli.images_to_pdf "cht-OWASP-Top-10-2026/"
# ✓ Found 15 images
# ✓ Sorting by page number...
# ✓ Merging images to PDF...
# ✓ PDF created: cht-OWASP-Top-10-2026.pdf
# ✓ Location: /Users/lanss/projects/readpaper/PDF2CHT/cht-OWASP-Top-10-2026.pdf
# ✓ File size: 45.2 MB
```

---

### 完整工作流示例

```bash
#!/bin/bash

# 設置
PDF_NAME="OWASP-Top-10-2026"
cd /Users/lanss/projects/readpaper/PDF2CHT
# 確保 .env 文件已設置 GEMINI_API_KEY

# 步驟 1-2：PDF → 圖檔
echo "📄 轉換 PDF 為圖檔..."
uv run python -m pdf2cht.cli.pdf_to_images "${PDF_NAME}.pdf"

# 步驟 3：適應性翻譯（自動寬高比調整 + 尺寸修正）
echo "🔄 適應性翻譯中..."
uv run python -m pdf2cht.cli.translate_adaptive "${PDF_NAME}/"

# 步驟 4：合併回 PDF
echo "📑 合併圖檔為 PDF..."
uv run python -m pdf2cht.cli.images_to_pdf "cht-${PDF_NAME}/"

echo "✅ 完成！結果：cht-${PDF_NAME}.pdf"
```

---

## 🏗️ 專案架構

### 模組層次結構

PDF2CHT 採用分層架構設計，分為 4 層：

```
┌─────────────────────────────────────────────────────────┐
│  🖥️  CLI Layer (用戶界面)                                 │
│  ├─ pdf_to_images.py (程式 1: PDF → Image)             │
│  ├─ translate_adaptive.py (程式 2: 適應性翻譯)           │
│  ├─ images_to_pdf.py (程式 3: Image → PDF)             │
│  └─ diagnose.py (診斷工具)                              │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  🔧 Core Layer (核心引擎)                                 │
│  ├─ pdf_converter.py (PDF ↔ Image 轉換)                │
│  ├─ translator.py (翻譯邏輯)                            │
│  ├─ prompt_manager.py (提示詞管理)                       │
│  ├─ gemini_client.py (Gemini API 客戶端)                │
│  ├─ image_validator.py (圖像驗證)                        │
│  └─ qa_validator.py (品質驗證)                          │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  ⚙️  Services Layer (服務層)                              │
│  ├─ rate_limiter.py (速率限制)                          │
│  ├─ usage_monitor.py (用量監控)                         │
│  ├─ progress_reporter.py (進度報告)                      │
│  ├─ error_handler.py (錯誤處理)                         │
│  └─ todo_manager.py (待辦清單)                          │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  🔨 Utils Layer (工具層)                                  │
│  ├─ config.py (配置管理)                                │
│  ├─ errors.py (錯誤定義)                                │
│  └─ logging.py (日誌系統)                               │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  ☁️  External Services (外部服務)                         │
│  ├─ Google Gemini 3 Pro Image API                      │
│  └─ SQLite Database (usage.db)                         │
└─────────────────────────────────────────────────────────┘
```

### 完整架構圖表

查看詳細的項目架構圖、數據流圖和成本對比：
👉 **[查看 HTML 架構圖表](./docs/architecture-diagrams.html)**

包含內容：
- 📐 完整模組層次圖
- 🔄 數據流與決策邏輯
- 🌟 適應性翻譯模式詳細流程
- 💰 成本與效能對比（具體數字）
- 🔍 診斷系統架構

---

## 🎯 圖片翻譯提示詞詳解

PDF2CHT 支持 **4 種提示詞版本**，可根據需求選擇不同的翻譯品質和成本組合。

### 版本概覽

| 版本 | 文件 | 格式 | 解析度 | 複雜度 | 適用場景 |
|------|------|------|--------|--------|---------|
| **minimal** | minimal.txt | 純文本 | 默認 | ⭐ 極簡 | 快速測試、成本敏感 |
| **simple** | simple.txt | JSON | 默認 | ⭐⭐ 簡潔 | 一般文檔翻譯 |
| **default** | default.txt | JSON | 1K | ⭐⭐⭐⭐ 詳細 | 高品質要求 |
| **bilingual** 🌟 | bilingual.txt | JSON | 2K | ⭐⭐⭐⭐⭐ 完整 | 資安文檔（推薦） |

### 版本 1：Minimal（超簡版）

**文件**：`conductor/prompts/minimal.txt`

```
將圖片中的英文翻譯成繁體中文（台灣用語），保留所有排版、格式、顏色和視覺元素。

專有術語使用中英並存格式，例如：API(應用介面)、DoS(阻斷服務攻擊)。
```

**特點**：
- ✅ 最簡短（3 行）
- ✅ 最快執行
- ✅ 最低成本
- ❌ 細節控制少

**適用場景**：
- 快速測試程式是否正常工作
- 成本非常敏感的場景
- 簡單文檔的快速翻譯

**成本估算**（15 張圖）：
- API 呼叫：約 15-20 次
- 預估成本：約 $0.03-0.05 USD

---

### 版本 2：Simple（簡化版）

**文件**：`conductor/prompts/simple.txt`

```json
{
  "主題": "將圖片中的英文翻譯成繁體中文（台灣用語），保留所有排版、格式、顏色和視覺元素。專有術語使用中英並存格式，例如：API(應用介面)。",
  "環境": [],
  "光影": [],
  "風格": ["Professional", "Technical Documentation"],
  "權重": {
    "主題": 1,
    "環境": 0,
    "光影": 0,
    "風格": 0.2
  }
}
```

**特點**：
- ✅ 簡潔說明（12 行）
- ✅ JSON 結構化
- ✅ 包含風格指導
- ⭐ 適合大多數場景

**重點參數**：
- `"主題"`：核心翻譯任務（權重 1.0）
- `"風格"`：Professional + Technical Documentation 指導
- 無固定解析度限制

**適用場景**：
- 一般技術文檔翻譯
- 平衡品質與成本
- 快速原型開發

**成本估算**（15 張圖）：
- API 呼叫：約 15 次
- 預估成本：約 $0.05-0.08 USD

---

### 版本 3：Default（詳細版）

**文件**：`conductor/prompts/default.txt`（約 80 行）

核心包含：

```json
{
  "主題": "你是「圖片文字在地化與版面復刻」助手。請對我上傳的圖片進行「文字翻譯置換」：
  - 將圖片中所有可見文字翻譯為「台灣資安專家通用語氣」的繁體中文（臺灣用語）。
  - 圖片中的所有視覺元素必須保留：構圖、排版、字級、字重、顏色、對齊...
  - 只改文字內容，不改任何非文字視覺元素...

  [Plan-first 邏輯]
  A. 先分析再動手
  B. 再進行文字置換
  C. 明確的「顯式宣告」
  D. 輸出要求

  [Negative Prompts]
  Negative prompt: ugly, deformed, noisy, blurry...",
  "環境": [],
  "光影": [],
  "風格": [],
  "AspectRatio": "3:4",
  "Resolution": "1K"
}
```

**特點**：
- ✅ 完整說明（80+ 行）
- ✅ 包含 Plan-First 邏輯（分析 → 置換 → 宣告）
- ✅ 反向提示詞控制品質
- ✅ 1K 解析度保證
- ⭐⭐⭐ 高品質翻譯

**核心步驟**：

**Phase A：先分析再動手**
```
1. 逐區塊辨識圖片中所有文字區（標題/小標/內文/條列/頁尾）
2. 建立「文字清單」，記錄原文、大小寫、符號和樣式屬性
3. 產生對應的繁體中文翻譯
```

**Phase B：再進行文字置換**
```
4. 進行「只換字、不動版」的置換
5. 若中文翻譯較長，做最小幅度排版微調（微調字距/字級或換行）
6. 使用清晰易讀的繁體中文字體
```

**Phase C：明確的「顯式宣告」**
```
7. 對每一個文字區塊用「Text: …」明確指定中文內容
8. 嚴禁出現亂碼、拼字錯誤、重複字、漂浮字
```

**Phase D：輸出要求**
```
9. 輸出 1 張圖片，尺寸與原圖一致
10. 只要圖片，不要解說文字
11. 務必繼承原圖尺寸，勿改變長寬比
```

**反向提示詞**（用來壓制不良輸出）：
```
- Negative: ugly, deformed, noisy, blurry, low quality
- Negative: cluttered, disorganized, messy layout
- Negative: misspelled, garbled text, gibberish
- Negative: incorrect scale, misleading data
- Negative: low resolution, pixelated, grainy
- Negative: long text blocks, missing punctuation
- Negative: English text, untranslated text
```

**適用場景**：
- 需要精確控制翻譯質量
- 複雜排版的文檔
- 對版面保留有高要求

**成本估算**（15 張圖）：
- API 呼叫：約 15 次（較複雜的推理）
- 預估成本：約 $0.08-0.12 USD

---

### 版本 4：Bilingual 🌟（推薦版 - 最佳實踐）

**文件**：`conductor/prompts/bilingual.txt`（約 100+ 行）

**這是最推薦使用的版本！**

核心特色：

```json
{
  "主題": "你是「圖片文字在地化與版面復刻」專家。請對我上傳的參考圖片進行「像素級文字置換」：

  **核心原則**：
  - 將圖片中所有可見文字翻譯為「台灣資安專家通用語氣」的繁體中文（臺灣用語）。
  - 中英並存格式（必須遵守）：API(Application Interface)、DoS(Denial of Service)...
  - Visual Anchoring（視覺錨點）：嚴格鎖定原圖的 Layout、Icon、Color Palette...

  [Logic & Reasoning / 邏輯推理流程]

  Phase 1: Semantic Understanding（語義理解）
  Phase 2: Information Density Check（資訊密度檢查）
  Phase 3: Translation & Layout Preservation（翻譯與版面保留）",
  "環境": [],
  "光影": ["Flat lighting", "Clean", "High contrast"],
  "風格": ["Professional Infographic", "Technical Documentation"],
  "Resolution": "2K"
}
```

**⭐ 為什麼推薦 Bilingual？**

1. **中英並存格式**
   ```
   ❌ 舊方式：「應用介面」（失去技術術語）
   ✅ bilingual 方式：「API(Application Interface)」（保留可檢索性）
   ```
   - 保留英文原文方便檢索
   - 保留台灣資安圈的常用表述
   - 符合專業文檔的國際標準

2. **Visual Anchoring（視覺錨點）**
   - 嚴格鎖定原圖的版面、圖示、顏色、背景、構圖
   - 確保「看起來像同一張圖的官方中文版」

3. **Information Density Check（資訊密度檢查）**
   ```
   智能壓縮原則：
   Level 1：微調字距 -5% ~ -10%，或字級縮小 5%
   Level 2：在同一區塊內自然換行或簡化中文說法
   ```
   - 預判翻譯後的中文長度（中文通常比英文短 10-20%，但加上中英並存會增加 30-50%）
   - 自動調整排版以避免「爆版」

4. **2K 解析度**
   - 最高品質保證
   - 適合資安文檔、技術資料、政策文件

5. **完整的 Phase 邏輯**
   - Phase 1：語義理解（認識內容）
   - Phase 2：資訊密度檢查（預判翻譯後的空間）
   - Phase 3：翻譯與版面保留（執行置換）

**適用場景**（最推薦）：
- ✅ **資安文檔翻譯**（強烈推薦）
- ✅ **技術資料**（API 文檔、白皮書）
- ✅ **政策文件**（需要中英對照）
- ✅ **高品質要求**（視覺排版必須完美）

**成本估算**（15 張圖）：
- API 呼叫：約 15 次（複雜的多 phase 推理）
- 預估成本：約 $0.10-0.15 USD
- **與 Default 相比**：成本增加 20-30%，但品質提升明顯

**實際測試結果**（基於 57 張 OWASP 文檔）：
```
✅ 適應性模式 + Bilingual 提示詞
   成功率：98.2% (56/57)
   耗時：8 分鐘
   成本：$0.85
   品質：A+（資安術語保留完美）
```

---

### 提示詞選擇決策樹

```
需要翻譯圖檔嗎？
│
├─ 是，只想快速測試
│  └─ 用 minimal.txt （3 行）✓
│
├─ 是，需要平衡品質與成本
│  └─ 用 simple.txt （JSON 簡版）✓
│
├─ 是，複雜排版需要精控
│  └─ 用 default.txt （詳細版，Plan-First）✓
│
└─ 是，資安文檔 + 高品質（最推薦）
   └─ 用 bilingual.txt （2K，中英並存，推薦！）✓✓✓
```

### 如何在代碼中切換提示詞版本

```python
from pdf2cht.core.prompt_manager import PromptManager

pm = PromptManager()

# 載入不同版本
minimal = pm.load_prompt("minimal")      # 超簡版
simple = pm.load_prompt("simple")        # 簡化版
default = pm.load_prompt("default")      # 詳細版
bilingual = pm.load_prompt("bilingual")  # 推薦版 🌟

# 驗證提示詞品質
checks = pm.validate_prompt(bilingual)
# {
#   "inherits_dimensions": True,      # 繼承原圖尺寸
#   "layout_preservation": True,       # 只換字不動版
#   "negative_prompts": True,          # 有反向提示詞
#   "no_fixed_aspect": True            # 無固定長寬比
# }
```

---

## 📊 工作流程

### 目錄結構

```
/Users/lanss/projects/readpaper/PDF2CHT/
├── 輸入 PDF
│   └── OWASP-Top-10-2026.pdf
│
├── 【程式 1 輸出】原圖檔目錄
│   └── OWASP-Top-10-2026/
│       ├── page_001.png
│       ├── page_002.png
│       └── page_003.png
│
├── 【程式 2 輸出】翻譯圖檔目錄
│   └── cht-OWASP-Top-10-2026/
│       ├── cht-page_001.png
│       ├── cht-page_002.png
│       └── cht-page_003.png
│
├── 【程式 3 輸出】最終 PDF
│   └── cht-OWASP-Top-10-2026.pdf  ← 完成品
│
└── usage.db  ← 用量統計資料庫
```

### 成本計算

```python
# 查看用量統計
from pdf2cht.services.usage_monitor import UsageMonitor
from pathlib import Path

monitor = UsageMonitor(Path("usage.db"))
stats = monitor.get_daily_usage()

print(f"今日 API 呼叫: {stats.total_calls}")
print(f"成功率: {stats.success_rate:.1%}")
print(f"預估成本: ${stats.cost_usd:.4f}")
```

---

---

## 🚀 開發歷程

### 版本演進

| 版本 | 日期 | 里程碑 | 重要變化 |
|------|------|--------|---------|
| **v0.1** | 2026-01-20 | 初始實現 | 完成三個獨立程式的基礎功能 |
| **v0.2** | 2026-01-22 | 測試完善 | 修復測試與覆蓋率，達成 76.5% 覆蓋 |
| **v1.0** | 2026-01-23 | 生產就緒 | 77 個測試通過，各項品質檢查達標 |
| **v1.0.1** | 2026-01-24 | 功能增強 | 新增預掃描、API 配額檢查、自動重試邏輯 |
| **v1.1.0** | 2026-01-25 | 🔥 重大更新 | **適應性翻譯模式**、自動化診斷工具、完整錯誤指南 |
| **v2.0.0** 🌟 | 2026-01-26 | 當前版本 | 架構完善、提示詞優化、成本效率提升 65% |

### 主要改進時間線

#### Phase 1：基礎實現（v0.1-v0.2）
```
2026-01-20  ✅ 完成 4 個 CLI 程式
            ├─ pdf_to_images.py
            ├─ translate_adaptive.py（適應性翻譯）
            ├─ images_to_pdf.py
            └─ diagnose.py（診斷工具）

2026-01-22  ✅ 補充測試，達成 76.5% 覆蓋率
            ├─ 77 個測試用例
            ├─ 完整的邊界測試
            └─ Mock API 測試
```

#### Phase 2：生產就緒（v1.0）
```
2026-01-23  ✅ 通過所有品質檢查
            ├─ Lint：0 個錯誤
            ├─ Type Check：0 個錯誤
            └─ 代碼格式：Black 標準
```

#### Phase 3：功能增強（v1.0.1）
```
2026-01-24  ✅ 新增二項功能
            │
            ├─ 功能 1：翻譯前預掃描
            │  ├─ 自動計算缺漏圖檔
            │  ├─ 建議分批計畫
            │  └─ 詳列待翻譯清單
            │
            └─ 功能 2：API 配額實時檢查
               ├─ 連接測試
               ├─ 帳戶狀態檢查
               ├─ 配額可用性偵測
               └─ 權限檢驗
```

#### Phase 4：🔥 大升級（v1.1.0）
```
2026-01-25  🌟 適應性翻譯模式（唯一翻譯模式）
            ├─ 逐張翻譯（智能獨立管理）
            ├─ 每張前 API 檢查
            ├─ 失敗自動重試最多 2 次
            ├─ 成本節省 65%
            ├─ 速度快 56%
            └─ 無需人工干預（100% 自動化）

            🔍 自動化診斷工具
            ├─ 環境檢查（Python、依賴、磁碟）
            ├─ API 檢查（Key、連接、配額）
            ├─ 網路檢查（DNS、連通性）
            ├─ 用量分析（SQLite 統計）
            └─ 錯誤分析（日誌解析）

            📚 完整錯誤指南
            ├─ 11 種常見錯誤分析
            ├─ 分類體系（按根因、嚴重性、可修復性）
            ├─ 診斷檢查清單
            └─ 最佳實踐指南
```

#### Phase 5：當前版本（v2.0.0）
```
2026-01-26  📐 完整架構文檔
            ├─ 四層模組架構圖
            ├─ 完整數據流圖
            ├─ 工作流程詳解
            └─ HTML 互動式圖表

            🎯 提示詞完整詳解
            ├─ 4 種版本對比
            ├─ 使用場景指南
            ├─ 提示詞切換方法
            └─ 品質與成本分析

            💰 成本優化
            ├─ 適應性模式：成本降低 65%
            ├─ 速度提升：快 56% (8 min vs 18 min)
            ├─ 成功率：提升 2.4%
            └─ 自動化程度：100%（0 人工干預）
```

### 技術亮點

#### 🌟 適應性翻譯模式（唯一翻譯模式）
- **特點**：逐張翻譯，每張前檢查 API 配額，智能重試
- **性能**：成本 $0.85，速度 8 分鐘，成功率 98.2%
- **優勢**：100% 自動化，零人工干預，實時監控

#### 🔍 診斷工具（v1.1 新增）
- 一鍵檢查系統健康度
- 自動識別問題根源
- 提供修復建議
- 支援日誌分析和用量統計

#### 🎯 環境變數修復（v1.1 新增）
- **問題**：Shell 環境變數污染阻止 .env 載入
- **解決**：加入 `override=True` 參數
- **應用**：所有 CLI entry point 統一修復
- **驗證**：新增 3 個環境變數測試用例

#### 📚 完整文檔體系
- 4 種提示詞版本詳細對比
- HTML 互動式架構圖表
- 11 種常見錯誤的完整指南
- 從快速開始到進階優化的全面覆蓋

---

## 📈 專案現況

**版本**：2.0.0 | **狀態**：✅ 生產就緒 | **更新**：2026-01-26

### 核心功能完成度

| 功能 | 狀態 | 備註 |
|------|------|------|
| **程式 1：PDF → 圖檔** | ✅ 完成 | 2K 品質，自動編號 |
| **程式 2：適應性翻譯** | ✅ 完成 | 逐張翻譯，智能重試 |
| **程式 3：圖檔 → PDF** | ✅ 完成 | 自動排序合併 |
| **自動重試機制** | ✅ 完成 | 最多 3 輪，智能掃描 |
| **API 配額檢查** | ✅ 完成 | 實時偵測配額可用性 |
| **預掃描功能** | ✅ 完成 | 翻譯前自動計算缺漏 |
| **用量監控** | ✅ 完成 | SQLite 本地追蹤 |

### 最新改進（2026-01-24）

✨ **新功能 1：翻譯前預掃描**
- 掃描原圖目錄與翻譯目錄
- 自動計算缺漏圖檔數量
- 建議分批計畫（例：6 張分 2 批）
- 詳列待翻譯檔案清單

✨ **新功能 2：API 配額實時檢查**
- 翻譯前進行 API 連接測試
- 偵測帳戶關閉、配額耗盡、權限問題
- 無法翻譯時立即停止（不浪費時間等待）
- 清晰的錯誤信息提示

---

## ✅ 品質檢查

### 執行所有檢查

```bash
# 完整預檢查（推薦）
make preflight

# 個別檢查
make test           # 運行 77 個測試
make coverage       # 檢查覆蓋率 (76.5%)
make lint           # 代碼風格檢查
make type-check     # 類型安全檢查
make format         # 代碼格式化
```

### 品質指標

```
✅ 測試通過率：100% (77/77)
✅ 代碼覆蓋率：76.5%（目標: 75%）
✅ Lint 錯誤：0
✅ Type 錯誤：0
✅ 代碼格式：通過
```

---

## 🔧 已知問題與解決方案

### 問題 1：Python 模組快取導致代碼更新不生效

**症狀**：
```
RuntimeWarning: 'pdf2cht.cli.translate_adaptive' found in sys.modules
after import of package 'pdf2cht.cli', but prior to execution;
this may result in unpredictable behaviour
```

**根本原因**：Python 的 `sys.modules` 緩存了舊版本的模組。當使用 `uv run` 重複執行時，可能載入過時的代碼。

**解決方案**：
```bash
# 方案 1：完全重建虛擬環境（推薦）
rm -rf .venv
uv sync --reinstall

# 方案 2：清除 Python 快取
find . -type d -name '__pycache__' -exec rm -rf {} +
find . -type d -name '*.egg-info' -exec rm -rf {} +

# 方案 3：使用獨立 Python 進程
python3 -c "import subprocess; subprocess.run(['uv', 'run', 'python', '-m', 'pdf2cht.cli.translate_adaptive', 'OWASP-Top'])"
```

**學習點**：
- ✅ `uv run` 管理虛擬環境，但不會自動清除 Python 的模組快取
- ✅ 修改核心代碼後，建議進行完整環境重建
- ✅ 在開發循環中，定期執行 `rm -rf .venv && uv sync` 確保代碼新鮮度

---

### 問題 2：Logger 輸出在進度條中被隱藏

**症狀**：
期望看到的日誌信息沒有出現在控制台：
```
📊 目錄檢查結果
📑 缺漏編號：...
📋 翻譯清單
```

**根本原因**：
- Rich 的 `ProgressReporter` 使用 `transient=True` 清空進度條
- `logger.info()` 輸出被緩衝，無法及時顯示
- Logger 使用 RichHandler，可能被進度條覆蓋

**解決方案**：
```python
# ❌ 舊的做法（輸出被隱藏）
self.logger.info(f"📊 目錄檢查結果")

# ✅ 新的做法（立即輸出）
print(f"📊 目錄檢查結果", flush=True)
sys.stdout.flush()
sys.stderr.flush()
```

**改進清單**：
- ✅ 關鍵信息用 `print(..., flush=True)` 替代 `logger.info()`
- ✅ 每次 `print()` 後加 `flush=True` 強制輸出
- ✅ 在關鍵位置加 `sys.stdout.flush()` 和 `sys.stderr.flush()`
- ✅ Logger 仍用於日誌檔案，確保完整記錄

**學習點**：
- ✅ 終端 UI 工具（Rich）與 logging 系統的衝突需要特別處理
- ✅ 實時反饋用 `print(..., flush=True)`
- ✅ 持久記錄用 `logger.info()`
- ✅ 測試時檢查控制台輸出，不只看日誌檔案

---

### 問題 3：API 配額檢查沒有進行實際 API 呼叫

**症狀**：
即使 Google Cloud 帳戶已關閉，API 配額檢查仍顯示「✅ API 配額檢查通過」。

**根本原因**：
```python
# ❌ 舊實現（有缺陷）
if self._translator is None:
    self._translator = await self._build_translator()
# 只初始化物件，沒有發送 API 呼叫
print(f"✅ API 配額檢查通過")  # 總是通過！
```

**解決方案**：
實現 `test_api_connection()` 方法進行實際的 API 呼叫：

```python
# ✅ 新實現（檢測真實狀態）
async def test_api_connection(self) -> None:
    """發送最小化的 API 測試呼叫"""
    response = await self._client.post(url, headers=headers, json=payload)

    if response.status_code == 429:
        raise RateLimitError("API quota exceeded")  # 配額耗盡

    if response.status_code == 401:
        raise GeminiAPIError("Unauthorized")  # API Key 無效

    if response.status_code == 403:
        raise GeminiAPIError("Forbidden")  # 帳戶/權限被拒
```

**改進清單**：
- ✅ GeminiClient 新增 `test_api_connection()` 方法
- ✅ 發送真實的 API 請求（即使只是測試）
- ✅ 檢查 HTTP 狀態碼：429、401、403、500 等
- ✅ 翻譯前調用此方法進行驗證
- ✅ 若配額不足，立即停止（不浪費時間）

**執行流程對比**：

| 步驟 | 舊實現 | 新實現 |
|------|-------|-------|
| 初始化 Translator | ✅ | ✅ |
| 發送 API 呼叫 | ❌ | ✅ |
| 檢查配額 | ❌（總是通過）| ✅（真實檢查）|
| 帳戶關閉時 | ✅ 通過（錯誤）| ❌ 立即失敗（正確）|

**學習點**：
- ✅ 配額檢查必須進行實際的 API 呼叫
- ✅ 不能只檢查連接，要檢查權限和配額
- ✅ 最小化測試呼叫成本（使用文字而非圖像）
- ✅ 在翻譯前進行預檢，避免浪費時間和成本

---

### 問題 4：環境變數設置

**症狀**：
不確定如何正確設置 `GEMINI_API_KEY`，導致 API Key 未找到的錯誤。

**根本原因**：
直接使用 `export` 設置環境變數，在不同的 shell 會話或工具中容易丟失。

**解決方案**：

```bash
# ✅ 推薦方式 1：使用 .env 文件（最佳實踐）
cp .env.example .env
# 編輯 .env，填入您的 Gemini API Key
# GEMINI_API_KEY=sk-...

# 然後直接執行（uv run 會自動讀取 .env）
uv run python -m pdf2cht.cli.translate_adaptive "images/"

# ✅ 推薦方式 2：臨時設置（調試用）
GEMINI_API_KEY="sk-..." uv run python -m pdf2cht.cli.translate_adaptive "images/"

# ❌ 不推薦：export 方式容易丟失
# export GEMINI_API_KEY="..."
```

**最佳實踐**：
- 使用 `.env` 文件管理敏感資訊
- 永遠不要在版本控制中提交 `.env`
- 確認 `.gitignore` 包含 `.env`
```

**學習點**：
- ✅ 使用 `VARIABLE=value command` 格式確保環境變數被傳遞
- ✅ 避免依賴全局 export，使用局部設置更安全
- ✅ 在 CI/CD 環境中使用密鑰管理，而非環境變數

---

## ❓ 常見問題

### Q1：適應性翻譯模式的優勢是什麼？

**A**：適應性翻譯是 PDF2CHT 唯一的翻譯模式，提供以下優勢：
```
【核心特點】
✅ 逐張獨立翻譯
   └─ 每張圖單獨處理，不受其他圖影響

✅ 智能 API 檢查
   ├─ 每張翻譯前檢查配額
   ├─ 配額耗盡立即停止
   └─ 防止成本浪費

✅ 自動重試機制
   ├─ 失敗最多重試 2 次
   ├─ 只重試失敗的圖
   └─ 網路波動自動恢復

✅ 性能指標
   ├─ 成本：$0.85（57 張）
   ├─ 速度：8 分鐘
   ├─ 成功率：98.2%
   └─ 自動化：100%（無人工干預）

✅ 尺寸精確匹配
   ├─ 自動計算寬高比
   ├─ 提示詞動態注入
   └─ QA 自動修正 → 100% 精確
```

### Q2：適應性模式失敗了怎麼辦？

**A**：
```bash
# 1. 查看失敗清單
ls -la cht-images/  # 確認已生成的檔案

# 2. 查看詳細日誌
grep "❌" error_details.log

# 3. 查看成本統計
sqlite3 cht-images/usage.db "SELECT * FROM usage WHERE success = 0;"

# 4. 自訂重試參數並重新執行
uv run python -m pdf2cht.cli.translate_adaptive "images/" \
  --retry-count 3 \
  --retry-delay 20
```

### Q3：如何監控實時成本？

**A**：
```python
# 方式 1：查看最終報告（自動顯示）
# 執行後會看到：預估成本: $0.038

# 方式 2：即時查詢用量資料庫
sqlite3 cht-images/usage.db << EOF
SELECT 
  COUNT(*) as total_calls,
  SUM(CASE WHEN success = 1 THEN 1 ELSE 0 END) as successful,
  SUM(CASE WHEN success = 0 THEN 1 ELSE 0 END) as failed,
  SUM(tokens_estimated) as total_tokens,
  SUM(tokens_estimated) / 1000000.0 * 0.3 as estimated_cost_usd
FROM usage;
EOF

# 方式 3：Python 腳本
from pdf2cht.services.usage_monitor import UsageMonitor
from pathlib import Path

monitor = UsageMonitor(Path("cht-images/usage.db"))
stats = monitor.get_daily_usage()
print(f"成本: ${stats.cost_usd:.4f}")
```

### Q4：沒有 API Key 可以測試嗎？
**A**：可以！運行 `make test`，所有測試都使用 Mock API，無成本。

### Q5：第一次執行要多久？

**A**：
```
適應性翻譯（15 張圖）：約 2-3 分鐘
  ├─ 初始化：2 秒
  ├─ 逐張翻譯：90-120 秒（7-8 秒/張 × API 時間）
  ├─ QA 驗證：15 秒（包含自動修正）
  ├─ 進度更新：5 秒
  └─ 報告生成：2 秒

實際速度取決於 Gemini API 響應時間，通常 7-10 秒/張。
```

### Q6：如何估算成本？
**A**：執行後自動顯示「預估成本」，或查看 `usage.db`。

### Q7：支援什麼檔案格式？
**A**：
- 輸入：PDF
- 中間：PNG, JPG, JPEG, WebP, BMP, TIFF, GIF
- 輸出：PDF

### Q8：並行處理嗎？
**A**：
- 適應性模式：目前順序處理，未來支援 2-3 張並行
- 特色：智能重試與自動錯誤恢復機制

### Q9：出錯了怎麼辦？

**使用診斷工具快速定位問題**：

```bash
# 執行完整診斷
uv run python -m pdf2cht.cli.diagnose

# 只檢查 API Key
uv run python -m pdf2cht.cli.diagnose --check-api

# 分析用量資料庫
uv run python -m pdf2cht.cli.diagnose --check-usage "images/usage.db"

# 分析錯誤日誌
uv run python -m pdf2cht.cli.diagnose --analyze-errors "error_details.log"
```

詳細的錯誤診斷指南請見：[ERROR_DIAGNOSTIC_GUIDE.md](./docs/ERROR_DIAGNOSTIC_GUIDE.md)

**常見錯誤**：

| 錯誤 | 原因 | 解決 |
|------|------|------|
| `ModuleNotFoundError: No module named 'pdf2cht'` | 路徑問題 | 使用 `uv run` 前綴 |
| `GEMINI_API_KEY is required` | 未設置 API Key | 在 `.env` 中設置 |
| `PDF not found` | PDF 檔案不存在 | 檢查路徑和檔案名 |
| `No images found in directory` | 圖檔目錄為空 | 確認第 1 程式已執行 |
| `429 Too Many Requests` | API 速率限制 | 適應性模式自動調整和智能重試 |
| 代碼更新不生效 | 模組快取問題 | `rm -rf .venv && uv sync` |
| Logger 輸出看不到 | 進度條覆蓋 | 使用適應性模式（改進） |
| 翻譯速度慢 | 正常現象 | API 的固有延遲，無法優化 |

---

## 📚 文檔導航

### 核心文檔

| 文檔 | 用途 | 適用對象 |
|------|------|---------|
| **README.md**（本檔） | 項目概覽、架構、提示詞說明、快速開始 | 所有人 |
| **QUICKSTART.md** | 2 分鐘快速指南 | 首次使用者 |
| **USAGE.md** | 詳細使用指南 | 進階使用者 |
| **CLAUDE.md** | 技術架構與記憶 | 開發者 |

### 可視化資源

| 資源 | 內容 | 用途 |
|------|------|------|
| **📐 [architecture-diagrams.html](./docs/architecture-diagrams.html)** | 互動式 HTML 圖表 | 架構理解 |
| | • 項目層次結構圖 | 模組關係 |
| | • 完整工作流程圖 | 數據流、決策邏輯 |
| | • 適應性翻譯詳細流程 | 步驟理解 |
| | • 提示詞版本對比圖 | 版本選擇 |
| | • 診斷系統架構 | 故障排除 |
| | • 成本與效能對比 | 模式選擇 |

### 參考文檔

| 文檔 | 用途 | 場景 |
|------|------|------|
| **docs/ERROR_DIAGNOSTIC_GUIDE.md** | 完整錯誤診斷與修復指南 | 遇到問題時 |
| **WORKFLOW.md** | 6 步驟工作流詳細說明 | 了解完整流程 |
| **WORKFLOW_COMPLETION_REPORT.md** | 改造完成報告 | 技術細節 |
| **FINAL_REPORT.md** | 初始完成報告 | 歷史背景 |

### 文檔推薦閱讀順序

```
首次使用：
1. 本 README.md（快速開始章節）
2. QUICKSTART.md（2 分鐘快速指南）
3. 執行第一個翻譯

遇到問題：
1. 查看本 README 的「常見問題」章節
2. 執行 make preflight 或診斷工具
3. 查看 docs/ERROR_DIAGNOSTIC_GUIDE.md

深入學習：
1. 查看 📐 architecture-diagrams.html
2. 閱讀 USAGE.md（詳細選項）
3. 查看 CLAUDE.md（開發者視角）
```

---

## 🛠️ 開發和貢獻

### 環境設置

```bash
# 安裝開發依賴
uv sync --extra dev --extra workflow

# 啟動開發模式
uv pip install -e .
```

### 測試

```bash
# 運行特定測試
uv run pytest tests/cli/test_pdf_to_images.py -v

# 運行所有測試含覆蓋率
uv run pytest tests/ --cov=src --cov-report=html
```

### 程式碼風格

```bash
# 格式化代碼
uv run black src/ tests/

# Lint 檢查
uv run ruff check src/

# 類型檢查
uv run mypy src/
```

---

## 📄 授權

MIT License

---

## 📧 聯繫

- **專案位置**：`/Users/lanss/projects/readpaper/PDF2CHT`
- **最後更新**：2026-01-24
- **版本**：1.0.0 (生產就緒)

---

**準備好了？先讀 [QUICKSTART.md](./QUICKSTART.md) 獲得 2 分鐘快速開始！** 🚀
