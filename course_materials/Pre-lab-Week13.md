# Week 13 Pre-Lab: Google Earth Engine & Cloud-Scale Time Series — ARIA v9.0 Setup

**Course:** NTU Remote Sensing & Spatial Information Analysis (遙測與空間資訊之分析與應用)  
**Instructor:** Prof. Su Wen-Ray  
**Week:** 13 | **Theme:** Google Earth Engine & Cloud-Scale Time Series Analysis（雲端運算與多時相分析）  
**Time Required:** ~25 minutes

---

## Objectives

By the end of this pre-lab, you will:
- Create or verify your Google Earth Engine (GEE) account
- Install the `earthengine-api` and `geemap` Python packages
- Understand the conceptual leap from **single-scene analysis** to **cloud-scale time series**（從單景分析到雲端時序分析）
- Review GEE's core abstractions: `Image`, `ImageCollection`, `Reducer`
- Understand InSAR fundamentals as a conceptual reference（InSAR 基礎概念，不實作）

---

## Step 1: Create / Verify GEE Account

### 1a. Register for Google Earth Engine

GEE is free for research and education.（GEE 對學術與教育用途免費。）

1. Go to [https://code.earthengine.google.com/register](https://code.earthengine.google.com/register)
2. Sign in with your Google account
3. Accept the Terms of Service
4. Create a **Cloud Project** (you can use an existing Google Cloud project or create a new one — the free tier is sufficient)（免費額度即足夠本課使用。）

> **Important:** GEE account approval is usually instant, but occasionally takes up to 24 hours. Do this step **before class**.（帳號通常即時核准，但偶爾需要最多 24 小時，請務必在上課前完成。）

### 1b. Verify Access

After registration, visit [https://code.earthengine.google.com/](https://code.earthengine.google.com/) and confirm you can see the Code Editor interface. You do not need to use the Code Editor for this course — we will use the Python API — but seeing the editor confirms your account is active.

---

## Step 2: Install Python Packages & Authenticate

This week **does not require a GPU**. All computation happens on Google's cloud servers; your local machine only sends commands and displays results.（本週不需要 GPU。所有運算在 Google 雲端完成，本機只負責送指令和顯示結果。）

### 2a. Install Packages

```bash
# Local users (VS Code / Windsurf / Terminal) — install via pip
pip install earthengine-api geemap
```

```python
# Verify installation
import ee
import geemap

print("✓ earthengine-api:", ee.__version__)
print("✓ geemap:", geemap.__version__)
```

> **Colab users:** `earthengine-api` and `geemap` are pre-installed, skip the pip install.

### 2b. Authenticate — Choose Your Environment（依你的開發環境選擇驗證方式）

GEE 驗證只需做一次，之後 token 會存在本機（`~/.config/earthengine/`）。但不同開發環境的驗證方式不同，請依你的環境選擇：

#### Option A: Google Colab（推薦初學者）

在 Colab 上最簡單，直接在 notebook cell 執行：

```python
ee.Authenticate()
ee.Initialize(project='your-project-id')  # 替換為你的 Cloud Project ID
print("✓ GEE authenticated and initialized")
```

Colab 會自動跳出 Google 登入視窗，按提示授權即可。Token 會自動存入 Colab runtime。

#### Option B: VS Code / Windsurf / Cursor（本機 IDE + Jupyter Kernel）

> **⚠️ 已知問題：** VS Code、Windsurf、Cursor 等 IDE 的 Jupyter kernel **不支援 `input()` 互動輸入**。直接在 notebook cell 執行 `ee.Authenticate()` 會卡住，因為它需要你貼上驗證碼，但 IDE 不會顯示輸入框。

**解決方式：先在 Terminal 完成驗證，再回 notebook 使用。**

**Step 1 — 開啟 Terminal（終端機）：**
- VS Code: 上方選單 `Terminal` → `New Terminal`，或快捷鍵 `` Ctrl+` ``
- Windsurf: 同 VS Code，底部 Terminal panel
- macOS 系統 Terminal 或 Windows PowerShell 也可以

**Step 2 — 在 Terminal 執行驗證指令：**

```bash
earthengine authenticate
```

這會開啟瀏覽器，引導你登入 Google 帳號並授權。授權完成後，Terminal 會顯示 `Successfully saved authorization token.`

> **如果出現 `command not found: earthengine`：**  
> 表示 `earthengine-api` 尚未安裝，或安裝路徑不在 PATH 中。請先執行：
> ```bash
> pip install earthengine-api
> ```
> 如果用 conda，改用 `conda install -c conda-forge earthengine-api`。  
> 安裝完後可能需要重新開啟 Terminal 才能找到指令。

**Step 3 — 回到 notebook，只需 Initialize（不需再 Authenticate）：**

```python
import ee
ee.Initialize(project='your-project-id')  # 替換為你的 Cloud Project ID
print("✓ GEE initialized — token loaded from terminal authentication")
```

因為 token 已存在 `~/.config/earthengine/`，`ee.Initialize()` 會自動讀取，不需要再跑 `ee.Authenticate()`。

#### Option C: 純 Terminal / 命令列（不用 Jupyter）

如果你偏好用 `.py` 腳本而非 notebook：

```bash
# Step 1: Authenticate (only once)
earthengine authenticate

# Step 2: Run your script
python my_gee_script.py
```

腳本中同樣只需 `ee.Initialize(project='your-project-id')`。

#### 驗證方式比較表

| 環境 | 驗證方式 | 備註 |
|------|---------|------|
| **Google Colab** | `ee.Authenticate()` in notebook cell | 最簡單，自動彈出授權視窗 |
| **VS Code** | Terminal `earthengine authenticate` → notebook `ee.Initialize()` | IDE Jupyter 不支援 `input()`，必須用 Terminal |
| **Windsurf** | 同 VS Code | Windsurf 基於 VS Code，行為一致 |
| **Cursor** | 同 VS Code | Cursor 基於 VS Code，行為一致 |
| **JupyterLab（本機）** | `ee.Authenticate()` in notebook cell | 支援 `input()`，和 Colab 一樣簡單 |
| **PyCharm** | Terminal `earthengine authenticate` → script `ee.Initialize()` | PyCharm 的 Python Console 可能支援 `input()` |
| **Terminal + .py** | `earthengine authenticate` → `python script.py` | 適合自動化腳本 |

### 2c. Confirm Connection（驗證連線）

不管你用哪種方式驗證，最後都用這段程式碼確認 GEE 連線正常：

```python
# Quick test: get the elevation of Hualien
point = ee.Geometry.Point([121.6, 23.97])
dem = ee.Image('USGS/SRTMGL1_003')
elevation = dem.sample(point, 30).first().get('elevation').getInfo()
print(f"✓ GEE connected — Hualien elevation: {elevation} m")
```

如果印出海拔高度值（例如 `15 m`），表示你的 GEE 環境已就緒！

### 2d. Confirm Other Packages

```python
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

# These should already be available from W8–W12
print("✓ numpy:", np.__version__)
print("✓ All core dependencies loaded successfully")
```

### Troubleshooting（常見問題排解）

| 問題 | 原因 | 解決方式 |
|------|------|---------|
| `ee.Authenticate()` 卡住不動 | VS Code/Windsurf 的 Jupyter 不支援 `input()` | 改用 Terminal 執行 `earthengine authenticate` |
| `command not found: earthengine` | 未安裝 `earthengine-api` 或 PATH 未更新 | `pip install earthengine-api`，然後重開 Terminal |
| `ee.Initialize()` 報錯 `project not found` | Project ID 打錯或未啟用 Earth Engine API | 到 [Google Cloud Console](https://console.cloud.google.com/) 確認 project ID 並啟用 Earth Engine API |
| `HttpError 403: Earth Engine is not enabled` | Cloud Project 未開啟 Earth Engine API | 到 Cloud Console → APIs & Services → 搜尋 "Earth Engine" → Enable |
| `google.auth.exceptions.DefaultCredentialsError` | Token 過期或損壞 | 重新執行 `earthengine authenticate` |
| Colab 上 `ee.Authenticate()` 沒有跳視窗 | 瀏覽器擋了彈出視窗 | 允許 colab.research.google.com 的彈出視窗 |

---

## Step 3: The Conceptual Leap — From Single Scene to Time Series

### What W8–W12 achieved（W8–W12 做了什麼）

In previous weeks, we analyzed **one image at a time** using STAC API + local computation:

| Week | What we did | Limitation |
|------|-------------|------------|
| W8 | NDVI from one Sentinel-2 scene | One snapshot in time（只有一個時間點） |
| W9 | ΔNDVI between two dates | Only two dates; manual download |
| W10 | SAR backscatter from one scene | Single SAR acquisition |
| W12 | Classification from one scene | Classifier trained on one date |

**Core limitation:** We downloaded individual images, processed them locally, and could only work with a handful of scenes. What if you need 10 years of data? 500 images?（如果需要 10 年的資料、500 張影像呢？）

### What W13 upgrades（W13 升級什麼）

**Google Earth Engine** moves computation to the cloud. Instead of downloading images, you send analysis commands to Google's servers, which process petabytes of satellite data and return only the results.

```
W8–W12:  Download image → Process locally → One scene at a time
W13:     Send command → GEE processes 500+ images on cloud → Return result
```

**Upgrade path（升級邏輯）:**
```
v5.0 (W8)  → Spectral analysis: one image, one index
v6.0 (W9)  → Change detection: two images, one difference
v7.0 (W10) → SAR fusion: one optical + one SAR
v8.0 (W12) → Classification: one image, multiple bands → land cover map
v9.0 (W13) → Cloud time series: hundreds of images → temporal trends ⬆
```

---

## Step 4: GEE Core Concepts — Concept Review

### Image（影像）

An `ee.Image` is a single raster image with bands and metadata. Similar to what you loaded via STAC API, but it lives on Google's servers — you never download the raw pixels.

```python
# Example: one Sentinel-2 image
image = ee.Image('COPERNICUS/S2_SR_HARMONIZED/20240405T021601_20240405T022256_T51RUH')
print(image.bandNames().getInfo())  # ['B1', 'B2', ..., 'B12', 'SCL', ...]
```

### ImageCollection（影像集合）

An `ee.ImageCollection` is a stack of images — potentially thousands — filtered by location, date, and metadata. This is the key upgrade: instead of manually searching one image at a time (as in STAC API), you describe *what you want* and GEE finds all matching images.（不再一張一張搜尋，而是描述條件讓 GEE 自動篩選所有符合的影像。）

```python
# All Sentinel-2 images over Hualien (2020-2026), cloud cover < 40%
collection = (ee.ImageCollection('COPERNICUS/S2_SR_HARMONIZED')
    .filterBounds(ee.Geometry.Point([121.6, 23.97]))
    .filterDate('2020-01-01', '2026-03-31')
    .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 40)))

print(f"Found {collection.size().getInfo()} images")
```

### Reducer（聚合器）

A `Reducer` summarizes a collection into a single value or image. For example, `ee.Reducer.median()` computes the pixel-wise median across all images in a collection — effectively creating a cloud-free composite.（Reducer 把一疊影像聚合成單一影像，例如取中位數來消除雲的影響。）

| Reducer | What it does | Use case |
|---------|-------------|----------|
| `median()` | Pixel-wise median | Cloud-free composite（無雲合成影像） |
| `mean()` | Pixel-wise mean | Average reflectance |
| `min()` / `max()` | Pixel-wise min/max | Extreme value detection |
| `linearFit()` | Linear regression per pixel | Long-term trend analysis（長期趨勢分析） |

### Lazy Evaluation（延遲運算）

GEE uses **lazy evaluation**: nothing is computed until you explicitly request a result (e.g., `.getInfo()`, `geemap.Map`, or `.getDownloadURL()`). This means you can chain many operations without waiting — GEE only executes when you ask for the final output.（GEE 使用延遲運算：你可以串接很多操作，只有在要求最終結果時才真正計算。）

---

## Step 5: InSAR — Conceptual Reference (Not Hands-On)

### What is InSAR?（什麼是 InSAR？）

**InSAR (Interferometric SAR)** measures ground surface displacement by comparing the **phase** of two SAR acquisitions taken at different times. While W10 used SAR **amplitude** (backscatter intensity) to detect flooding, InSAR uses SAR **phase** to detect millimeter-scale ground movement — such as earthquake-triggered subsidence, landslide creep, or volcanic inflation.（InSAR 利用兩個 SAR 影像的相位差來量測地表位移，精度可達毫米等級。）

| | W10 SAR (Amplitude) | InSAR (Phase) |
|---|---|---|
| Measures | Backscatter intensity（回波強度） | Phase difference（相位差） |
| Detects | Surface roughness, flooding | Ground displacement（地表位移） |
| Precision | Qualitative（定性） | mm-level（毫米級定量） |
| Application | Flood mapping, landslide detection | Subsidence, landslide creep, fault slip |

### Why not in GEE?（為什麼 GEE 不能做 InSAR？）

GEE only provides Sentinel-1 **GRD** (Ground Range Detected) data — intensity only, no phase information. InSAR requires **SLC** (Single Look Complex) data with phase, which GEE does not host. InSAR processing also requires specialized algorithms (co-registration, interferogram generation, phase unwrapping) that are beyond GEE's computation model.（GEE 只提供強度資料，沒有相位資訊，也不支援 InSAR 所需的專用演算法。）

### Available Resources & Tools（可用資源與套件）

If you want to explore InSAR on your own, here are the key resources:

**Data sources:**
- ASF DAAC ([https://search.asf.alaska.edu/](https://search.asf.alaska.edu/)) — Sentinel-1 SLC data download
- UNAVCO / EarthScope — geodetic InSAR products
- COMET LiCSAR ([https://comet.nerc.ac.uk/COMET-LiCS-portal/](https://comet.nerc.ac.uk/COMET-LiCS-portal/)) — pre-processed Sentinel-1 interferograms

**Processing software:**
| Tool | Type | Notes |
|------|------|-------|
| **ESA SNAP** | Desktop GUI + CLI | Free, official ESA toolbox, good for beginners |
| **ISCE2** | Python library | NASA JPL, research-grade, command-line |
| **MintPy** | Python library | Time-series InSAR (PS-InSAR, SBAS), built on top of ISCE2 |
| **GMTSAR** | CLI | GMT-based, well-documented tutorials |
| **LiCSBAS** | Python library | Works with COMET LiCSAR products, easiest entry point |

**Recommended learning path:**
1. Start with ESA SNAP tutorials (visual, step-by-step)
2. Try LiCSBAS with pre-processed LiCSAR products (no SLC processing needed)
3. For research: ISCE2 + MintPy for time-series InSAR

**Recommended readings（延伸閱讀）:**
- [InSAR 彩虹干涉環判讀](https://tech.ardswc.gov.tw/EPaper/Home/EPaper?PaperID=97f62ecf-44eb-4f41-a5a7-5a1d4048ef67) — 農村發展及水土保持署技術電子報第 141 期（2025）。用 Kīlauea 火山和 2016 熊本地震案例，圖解四步驟干涉環判讀法：確認衛星波段 → 計算干涉環數 → 判斷接近/遠離方向 → 估算位移量。中文，入門必讀。
- [大型山崩判釋新利器：結合 InSAR 與光達數值地形](https://www.ceci.org.tw/Upload/Download/F45F3A64-D098-465A-9CDB-F2A4B7A9DD10.pdf) — 陳柔妃、林慶偉（2018），中華技術期刊第 119 期。以南投縣定遠新村為例，說明如何結合 TCP-InSAR 地表變形分析與 LiDAR DEM 地形特徵判釋，修正大型山崩潛勢區範圍。台灣本土案例，與本課防災主題直接相關。
- 水保署技術電子報 InSAR 系列：[第 84 期](https://tech.ardswc.gov.tw/EPaper/Home/EPaper?PaperID=30781387-f5d7-4ce9-8484-816b15841fba)（SAR 波段介紹）、[第 120 期](https://tech.ardswc.gov.tw/EPaper/Home/EPaper?PaperID=30080cd6-2491-42f7-b744-034c838eefd7)（D-InSAR 與 PS-InSAR 基本概念）、[第 126 期](https://tech.ardswc.gov.tw/EPaper/Home/EPaper?PaperID=de1a4386-5f25-4b33-bf0d-ffc71aa121c9)（MT-InSAR + INV 崩塌預警）

> **Note:** InSAR processing is computationally intensive and requires significant disk space (SLC files are 4–8 GB each). It is a full course topic on its own. This introduction is meant to give you awareness of the technique so you can explore it independently if relevant to your research.（InSAR 處理需要大量計算與儲存空間，本身足以開設一門完整課程。此處僅提供概念介紹，供有興趣的同學自行深入。）

### Self-Test Q1

What is the key difference between SAR amplitude analysis (W10) and InSAR?

**Answer:** SAR amplitude measures backscatter intensity (surface roughness, moisture), useful for qualitative detection of flooding or landslides. InSAR measures phase difference between two acquisitions, enabling quantitative measurement of ground displacement at millimeter precision — useful for monitoring subsidence, landslide creep, and fault slip.（SAR 振幅分析量測回波強度，用於定性偵測；InSAR 量測相位差，能做到毫米級定量位移監測。）

---

## Step 6: GEE vs STAC API — Concept Review

### When to use which?（什麼時候用哪個？）

| | STAC API (W8–W12) | GEE (W13+) |
|---|---|---|
| Computation | Local (your machine) | Cloud (Google servers) |
| Data access | Download → process | Process on server → download result |
| Strength | Full control, any algorithm | Massive scale, petabyte catalog |
| Limitation | Limited by your RAM/disk | Limited to GEE-supported operations |
| Best for | Custom analysis, ML training | Time series, large-area monitoring |
| Python package | `pystac_client` + `stackstac` | `earthengine-api` + `geemap` |

**Key insight:** They are complementary, not competing. Use GEE when you need to process hundreds of images over large areas. Use STAC API when you need fine-grained control over individual scenes (e.g., training a classifier with specific band combinations).（兩者互補，不是競爭。大規模時序用 GEE，精細單景分析用 STAC API。）

### Self-Test Q2

You need to compute the average NDVI for Hualien County for every month from 2015 to 2024 (120 monthly composites from hundreds of Sentinel-2 images). Would you use STAC API or GEE? Why?

**Answer:** GEE. Downloading hundreds of images locally would require terabytes of storage and days of processing. GEE processes the images on its servers and returns only the 120 monthly NDVI values — the heavy computation never touches your machine.（GEE。下載數百張影像需要 TB 級儲存空間和數天處理時間，GEE 在雲端處理後只回傳 120 個月均值。）

---

## Step 7: Self-Test — Synthesis

### Q3: GEE Core Concepts

Match each GEE concept with its description:

| Concept | Description |
|---------|-------------|
| `ee.Image` | _____ |
| `ee.ImageCollection` | _____ |
| `ee.Reducer` | _____ |
| Lazy evaluation | _____ |

Options:
- A. Summarizes many values into one (e.g., median, mean, linear fit)
- B. A single raster image with bands and metadata
- C. A stack of images filtered by location, date, and metadata
- D. Computation only happens when you request the final result

**Answers:** Image = B, ImageCollection = C, Reducer = A, Lazy evaluation = D.

### Q4: Time Series Thinking

Why does computing a **median** composite from 50 images produce a nearly cloud-free result, even if each individual image has 20–40% cloud cover?

**Answer:** Clouds appear in different locations in each image. For any given pixel, most of the 50 observations are cloud-free. The median automatically picks a "typical" cloud-free value, because clouds (very bright) and cloud shadows (very dark) are statistical outliers that the median ignores.（雲在每張影像中出現的位置不同，取中位數時，雲和雲影都是統計離群值，會被自動排除。）

### Q5: InSAR Awareness

Can you do InSAR processing in Google Earth Engine? Why or why not?

**Answer:** No. GEE only hosts Sentinel-1 GRD data (intensity only), not SLC data (which contains phase information needed for interferometry). InSAR also requires specialized algorithms like co-registration and phase unwrapping that are not supported in GEE. For InSAR, you need tools like ESA SNAP, ISCE2, or MintPy with SLC data from ASF DAAC.（不行。GEE 只有 GRD 強度資料，沒有 InSAR 所需的 SLC 相位資料，也不支援干涉處理演算法。）

---

## Step 8: Reflection Questions (Optional)

1. **Scale thinking:** W9 computed ΔNDVI between two dates. What if you computed ΔNDVI between every consecutive month for 10 years — could you detect seasonal patterns and long-term trends? What new insights would that reveal compared to a two-date comparison?（如果計算 10 年每月的 ΔNDVI，能發現什麼新的資訊？）

2. **Cloud computing trade-offs:** GEE is powerful but you cannot install arbitrary Python packages or run custom deep learning models on it. When would the STAC API approach (W8–W12) still be the better choice?（什麼情況下 STAC API 仍然是更好的選擇？）

3. **InSAR + GEE integration:** Some researchers process InSAR externally (using SNAP/ISCE2) and then import the deformation maps into GEE for large-scale analysis. Why might this hybrid approach be useful?（為什麼有人會在外部做 InSAR 處理後再匯入 GEE？）

---

## Checklist Before Class

- [ ] GEE account registered and verified (can access [code.earthengine.google.com](https://code.earthengine.google.com))
- [ ] `earthengine-api` and `geemap` installed (`pip install earthengine-api geemap`)
- [ ] `ee.Authenticate()` completed successfully
- [ ] `ee.Initialize(project='...')` works (quick test returns elevation value)
- [ ] Understand GEE core concepts: Image, ImageCollection, Reducer, lazy evaluation
- [ ] Understand the difference between GEE (cloud) and STAC API (local)
- [ ] Understand InSAR concept (phase-based displacement measurement, not available in GEE)
- [ ] Know where to find InSAR resources (ASF DAAC, SNAP, ISCE2, MintPy)
- [ ] Completed Self-Test (5 questions)

**You're ready for Week 13!**

---

*Note: If you encounter any environment issues, post on NTUCool or email Prof. Su before class. The most common issue is GEE account activation — please complete Step 1 at least 24 hours before class.*
