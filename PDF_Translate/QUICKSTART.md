# 🚀 PDF2CHT - 快速開始 (2 分鐘)

## ⚡ 最快方式（Shell Scripts）

```bash
# 1. 進入項目目錄
cd /Users/lanss/projects/readpaper/PDF2CHT

# 2. 確保設置了 GEMINI_API_KEY（在 .env 或環境變數中）
export GEMINI_API_KEY="your_actual_key_here"
# 或編輯 .env: GEMINI_API_KEY=your_actual_key_here

# 3. 測試 API 連線
./run1.sh

# 4. 執行翻譯工作流
# 步驟 1: PDF → 圖檔
./run2.sh "OWASP-Top-10-2026.pdf"

# 步驟 2: 翻譯圖檔（適應性模式）
./run3.sh "OWASP-Top-10-2026/"

# 步驟 3: 合併回 PDF
./run4.sh "cht-OWASP-Top-10-2026/"

# 完成！檢查結果
ls -lh cht-OWASP-Top-10-2026.pdf
```

## 🧪 測試（無 API 成本）

```bash
# 運行所有測試（使用 Mock API）
make test

# 檢查覆蓋率
make coverage

# 一鍵全檢查
make preflight
```

## 📋 執行方式對比

| 方式 | 命令 | 優點 | 適用場景 |
|------|------|------|--------|
| **1. Shell Scripts（推薦）** | `./run1.sh`<br/>`./run2.sh "file.pdf"`<br/>`./run3.sh "dir/"`<br/>`./run4.sh "cht-dir/"` | 最簡單、自動驗證、清晰錯誤提示 | 日常使用、新手友善 |
| **2. 直接 CLI** | `uv run python -m pdf2cht.cli.*` | 完整控制、進階選項 | 自訂參數、整合腳本 |
| **3. Python API** | `from pdf2cht.cli import ...` | 編程靈活性 | 系統整合、開發測試 |

## 🛠️ 完整準備檢查清單

```bash
# 1. 驗證環境
python -c "from src.pdf2cht.config import Config; Config()" && echo "✓"

# 2. 驗證依賴
python -c "import httpx, PIL" && echo "✓"

# 3. 運行測試
make test && echo "✓"

# 4. 準備輸入
mkdir -p images
# 放入 PNG/JPG 到 images/ 目錄

# 5. 開始翻譯
python -m pdf2cht images/
```

## 📁 目錄結構

```
PDF2CHT/
├── images/              # 輸入圖片目錄 (自建)
│   ├── image1.png
│   └── image2.jpg
├── output_cht/          # 輸出目錄 (自動建立)
│   ├── image1.png
│   └── image2.jpg
├── src/pdf2cht/         # 核心代碼
├── tests/               # 測試
├── USAGE.md             # 完整使用指南
├── FINAL_REPORT.md      # 完成報告
└── examples_quick_start.py  # 示例代碼
```

## 🔑 環境變數

```bash
# 必需
export GEMINI_API_KEY="your_key_here"

# 可選
export OUTPUT_DIR="/custom/output"      # 預設: ./output_cht
export LOG_LEVEL="INFO"                 # 預設: INFO
export MAX_RETRIES="3"                  # 預設: 3
```

## 📊 檢查成本與進度

```python
# 查看用量統計
from src.pdf2cht.services.usage_monitor import UsageMonitor
from pathlib import Path

monitor = UsageMonitor(db_path=Path("usage.db"))
stats = monitor.get_daily_usage()
print(f"Today's cost: ${stats['estimated_cost_usd']:.4f}")
```

## 🎯 常用命令速查

```bash
# 測試與驗證
make test              # 運行所有測試
make coverage          # 檢查覆蓋率
make lint              # 代碼風格檢查
make preflight         # 一鍵全檢查

# 執行
python -m pdf2cht images/          # 使用 CLI
./convert_to_cht.sh                # 使用 Bash
python examples_quick_start.py 1   # 單張示例
python examples_quick_start.py 2   # 批次示例

# 開發
uv sync                # 安裝依賴
python -m pytest -v    # 詳細測試
```

## 📖 詳細文檔

- **執行指南**: [USAGE.md](./USAGE.md)
- **完成報告**: [FINAL_REPORT.md](./FINAL_REPORT.md)
- **系統設計**: [conductor/SDD_TRANSLATOR_SYSTEM.md](./conductor/SDD_TRANSLATOR_SYSTEM.md)
- **實作計畫**: [conductor/tracks/init_translator_20240123/plan.md](./conductor/tracks/init_translator_20240123/plan.md)

## ❓ 常見問題 (FAQ)

**Q: 沒有 API Key 可以測試嗎？**
A: 可以！運行 `make test`，所有測試都使用 Mock API。

**Q: 第一次執行要多久？**
A: 取決於圖片數量和批次大小。通常 5 張圖片約 2-3 分鐘（包括 API 呼叫與冷卻時間）。

**Q: 如何估算成本？**
A: 運行後查看 `usage.db` 或執行上面的 Python 代碼。

**Q: 支援什麼格式？**
A: PNG, JPG, JPEG, WebP, BMP, TIFF, GIF

**Q: 可以並行處理嗎？**
A: 目前順序處理，已實現批次調度與速率限制。未來可擴展為並行。

## ✨ 下一步

1. ✅ 設置 API Key
2. ✅ 準備輸入圖片 (images/ 目錄)
3. ✅ 運行: `python -m pdf2cht images/`
4. ✅ 檢查結果: `output_cht/` 目錄
5. ✅ 查看成本: `usage.db`

---

**需要幫助？** 參考 [USAGE.md](./USAGE.md) 了解詳細使用方法。

**想測試？** 運行 `make test` (無 API 成本)

**想學習？** 查看 [examples_quick_start.py](./examples_quick_start.py)

---

🎉 **準備好了！祝翻譯成功！**
