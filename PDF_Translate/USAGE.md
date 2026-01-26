# 📖 PDF2CHT 系統 - 執行與使用指南

---

## 🚀 快速開始 (5 分鐘)

### 1️⃣ 環境準備

```bash
# 進入項目目錄
cd /Users/lanss/projects/readpaper/PDF2CHT

# 確保已安裝依賴
uv sync

# 驗證環境 (運行所有測試)
make preflight
```

**預期結果**:
```
✅ 51 tests passed
✅ Coverage: 88.57%
✅ No lint errors
✅ Type check passed
```

### 2️⃣ 設置 API Key

```bash
# 設置 Gemini API Key (必需以連接真實 API)
export GEMINI_API_KEY="your_actual_gemini_api_key_here"

# 驗證配置
python -c "from src.pdf2cht.config import Config; Config()"
# 如果沒有錯誤，表示配置成功
```

### 3️⃣ 準備輸入文件

```bash
# 方式 1: 使用現有 Bash 腳本 (快速測試)
ls -la *.pdf  # 確保有 PDF 文件

# 方式 2: 使用 Python API (推薦)
# 準備 PDF 文件在任意目錄，例如:
# /path/to/your/input.pdf
```

---

## 🎯 三種使用方式

### 方式 1️⃣: 使用現有 Bash 腳本 (快速)

**適用於**: 快速測試、簡單單張圖片轉換

```bash
# 確保有 PNG/JPG 圖片在當前目錄
ls *.png *.jpg

# 運行 Bash 腳本
export GEMINI_API_KEY="your-key"
./convert_to_cht.sh

# 查看結果
ls cht-*.png
ls cht-*.jpg
```

**特點**:
- ✅ 快速簡單
- ✅ 無依賴問題
- ❌ 無錯誤恢復
- ❌ 無批次管理
- ❌ 無用量監控

---

### 方式 2️⃣: 使用 Python 直接 API (推薦)

**適用於**: 程式化使用、批次處理、用量監控

```python
"""簡單的翻譯示例"""
import asyncio
from pathlib import Path
from src.pdf2cht.config import Config
from src.pdf2cht.core.gemini_client import GeminiClient
from src.pdf2cht.core.prompt_manager import PromptManager
from src.pdf2cht.core.qa_validator import QAValidator
from src.pdf2cht.core.translator import Translator

async def translate_single_image():
    # 1. 初始化配置與客戶端
    config = Config()  # 自動讀取 GEMINI_API_KEY

    gemini = GeminiClient(api_key=config.gemini_api_key)
    prompt_mgr = PromptManager()
    qa_validator = QAValidator()

    # 2. 建立協調器
    translator = Translator(
        gemini_client=gemini,
        prompt_manager=prompt_mgr,
        qa_validator=qa_validator
    )

    # 3. 準備輸入
    image_path = Path("input_image.png")
    output_dir = Path("output_cht")
    output_dir.mkdir(exist_ok=True)

    # 4. 執行翻譯
    try:
        result = await translator.translate_images(
            images=[image_path],
            output_dir=output_dir,
            prompt_version="default"
        )
        print(f"✅ 翻譯成功: {result}")
    except Exception as e:
        print(f"❌ 翻譯失敗: {e}")
    finally:
        await gemini.aclose()

# 運行
if __name__ == "__main__":
    asyncio.run(translate_single_image())
```

**運行**:
```bash
# 保存上面的代碼為 example_single.py
python example_single.py
```

---

## 📋 使用情景與選擇

### 📌 情景 1: 快速測試 (1-2 張圖片)

**使用**: **方式 1 (Bash 腳本)**

```bash
# 最快的方式
./convert_to_cht.sh
```

**時間**: <1 分鐘

---

### 📌 情景 2: 小批量翻譯 (3-10 張圖片)

**使用**: **方式 2 (Python 單張)**

```python
# 保存為 translate_images.py
import asyncio
from pathlib import Path
from src.pdf2cht.core.gemini_client import GeminiClient
from src.pdf2cht.core.translator import Translator

async def main():
    config = ... # 參考方式 2
    translator = ... # 參考方式 2

    # 迴圈翻譯多張
    images = list(Path("input").glob("*.png"))
    for img in images:
        result = await translator.translate_images([img], Path("output"))
        print(f"✓ {img.name}")

asyncio.run(main())
```

**時間**: 數分鐘

---

### 📌 情景 3: 大批量翻譯 (100+ 張)

**使用**: **方式 3 (批次調度)**

```bash
# 使用批次系統的完整優勢
python example_batch.py
```

**特點**:
- ✅ 自動速率限制
- ✅ 用量監控
- ✅ 進度報告
- ✅ 錯誤隔離與重試

**時間**: 取決於批次大小與冷卻時間

---

## 🧪 測試與驗證

### 運行測試

```bash
# 運行所有測試 (推薦)
make test

# 運行特定測試
pytest tests/unit/test_gemini_client.py -v
pytest tests/integration/test_e2e_single_image.py -v

# 運行覆蓋率檢查
make coverage
```

### 驗證環境

```bash
# 1. 驗證配置
python -c "from src.pdf2cht.config import Config; print('✓ Config OK')"

# 2. 驗證依賴
python -c "import httpx, PIL; print('✓ Dependencies OK')"

# 3. 驗證 API Key
python -c "from src.pdf2cht.config import Config; Config(); print('✓ API Key OK')"

# 4. 一鍵驗證所有
make preflight
```

---

## 🔧 進階使用

### 自訂配置

```python
from src.pdf2cht.config import Config

# 自訂輸出目錄
import os
os.environ["OUTPUT_DIR"] = "/custom/output/path"

config = Config()
print(f"Output dir: {config.output_dir}")
```

### 自訂 Prompt

```python
from src.pdf2cht.core.prompt_manager import PromptManager
from pathlib import Path

# 建立自訂 Prompt
custom_prompt_path = Path("conductor/prompts/custom.txt")
custom_prompt_path.write_text("Your custom prompt here...")

# 使用自訂 Prompt
prompt_mgr = PromptManager()
prompt = prompt_mgr.load_prompt("custom")
```

## 📊 監控與日誌

### 即時監控

```python
from src.pdf2cht.services.progress_reporter import ProgressReporter
from src.pdf2cht.logging import get_logger

# 獲取日誌
logger = get_logger(__name__)
logger.info("Processing started...")

# 進度報告
reporter = ProgressReporter()
reporter.report_batch_start(batch_num=1, total_batches=10)
reporter.report_item_completed(item_num=5)
```

### 查看用量統計

```python
from src.pdf2cht.services.usage_monitor import UsageMonitor
from pathlib import Path
from datetime import datetime

monitor = UsageMonitor(db_path=Path("usage.db"))

# 查看今日統計
stats = monitor.get_daily_usage(datetime.today())
print(f"Today's calls: {stats['total_calls']}")
print(f"Success rate: {stats['success_rate']:.1%}")
print(f"Estimated cost: ${stats['estimated_cost_usd']:.4f}")
```

---

## 🐛 常見問題

### Q1: ImportError - 找不到 pdf2cht 模組

**症狀**:
```
ModuleNotFoundError: No module named 'pdf2cht'
```

**解決**:
```bash
# 確保在項目根目錄
cd /Users/lanss/projects/readpaper/PDF2CHT

# 重新安裝依賴
uv sync

# 或者添加項目到 Python 路徑
export PYTHONPATH="${PYTHONPATH}:$(pwd)/src"
```

---

### Q2: GEMINI_API_KEY 未設置

**症狀**:
```
ConfigError: GEMINI_API_KEY environment variable not set
```

**解決**:
```bash
# 設置 API Key
export GEMINI_API_KEY="your_actual_key_here"

# 驗證
echo $GEMINI_API_KEY
```

---

### Q3: 連接真實 API 時超時

**症狀**:
```
httpx.ConnectError: [Errno -2] Name or service not known
```

**解決**:
```bash
# 1. 檢查網絡連接
ping google.com

# 2. 驗證 API Key 有效性
# 訪問: https://aistudio.google.com/

# 3. 檢查防火牆/代理設置
# 如需代理：
export HTTP_PROXY="http://proxy:port"
export HTTPS_PROXY="http://proxy:port"
```

---

### Q4: 測試失敗 - 資源警告

**症狀**:
```
ResourceWarning: unclosed database
```

**解決**: 這是警告而非錯誤，不影響功能。測試仍通過。

---

## 📈 效能優化建議

### 1. 並行處理（未來功能）

```python
# 目前: 順序處理
# 批次大小: 5，冷卻時間: 120 秒

# 優化建議:
# - 增加批次大小至 10-20
# - 或縮短冷卻時間至 60 秒
# - 但需遵守 API 限制 (1000-2000 RPM)
```

### 2. 快取機制（未來功能）

```python
# 建議添加:
# - 本地圖片快取 (避免重複翻譯)
# - Prompt 快取 (避免重複加載)
```

### 3. 本地 GPU 加速（未來功能）

```python
# 若未來支援本地 OCR/QA:
# - 使用 GPU (如有可用)
# - 減少 API 呼叫次數
```

---

## 🚀 從 Mock 切換到真實 API

### 目前狀態: Mock 模式

```python
# 所有測試使用 Mock API
# 不消耗 API 額度
pytest tests/ -v
```

### 切換到真實 API

```python
# 1. 設置真實 API Key
export GEMINI_API_KEY="your_actual_key"

# 2. 確保有有效的 PDF/圖片
ls input_images/*.png

# 3. 運行翻譯
python example_single.py
# 或
python example_batch.py

# 4. 驗證結果
ls output_cht/
```

---

## 📞 支援與反饋

### 檢查系統狀態

```bash
# 一鍵診斷
make preflight

# 查看詳細日誌
pytest tests/ -v -s

# 查看覆蓋率報告
open htmlcov/index.html
```

### 查看關鍵文檔

```bash
# 系統設計
cat conductor/SDD_TRANSLATOR_SYSTEM.md | less

# 實作計畫
cat conductor/tracks/init_translator_20240123/plan.md | less

# 最終報告
cat FINAL_REPORT.md | less
```

---

## 📋 檢查清單 - 準備生產部署

```
準備工作:
[ ] 取得有效的 Gemini API Key
[ ] 設置環境變數: export GEMINI_API_KEY="..."
[ ] 運行測試驗證: make preflight
[ ] 準備輸入 PDF/圖片檔案
[ ] 建立輸出目錄: mkdir output_cht

執行選項:
[ ] 快速測試 (1-2 張): 使用方式 1 (Bash)
[ ] 小批量 (3-10 張): 使用方式 2 (Python)
[ ] 大批量 (100+): 使用方式 3 (批次系統)

後續步驟:
[ ] 監控用量統計
[ ] 驗證翻譯品質
[ ] 保存結果
[ ] 檢查成本
```

---

## ✨ 下一步

1. **選擇執行方式** (根據需求選擇方式 1/2/3)
2. **準備輸入** (PDF 或圖片)
3. **設置 API Key** (`export GEMINI_API_KEY=...`)
4. **運行翻譯**
5. **檢查結果** (`output_cht/` 目錄)
6. **監控用量** (database: `usage.db`)

---

**祝翻譯成功！** 🎉

有任何問題，請參考 FINAL_REPORT.md 或 SDD_TRANSLATOR_SYSTEM.md。

