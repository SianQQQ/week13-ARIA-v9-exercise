# 第 13 週作業 — ARIA v9.0 Cloud Engine

國立臺灣大學遙測與空間資訊分析  
第 13 週：Google Earth Engine 與雲端尺度時間序列分析

## 檔案

| 路徑 | 說明 |
|---|---|
| `notebooks/Week13_ARIA_v90_張育憲_Completed_executed.ipynb` | 主要繳交 notebook，已執行所有 cells 並保留輸出結果 |
| `notebooks/Week13_ARIA_v90_張育憲_Completed.ipynb` | 完成版原始 notebook，已填入所有空格與文字回答 |
| `notebooks/Week13-Student-original.ipynb` | 課程提供的原始學生作業 notebook |
| `course_materials/Pre-lab-Week13.md` | 課前檢查表與概念複習 |
| `course_materials/Week13-Slides.pptx` | 第 13 週課程投影片 |

## 完成內容

- S1：初始化 GEE 環境，使用 project `white-gate-489512-r8`。
- S2-S3：完成 Sentinel-2 影像集合篩選、SCL 遮罩與 NDVI 計算。
- S4-S4b：建立每月 NDVI mean/min/max 時間序列、變異幅度圖，並比較花蓮與太魯閣的區域尺度差異。
- S5：完成三個時期的 NDVI 中位數合成影像與 Delta-NDVI 變化圖。
- S6-S7：完成 Sentinel-1 VV 時間序列、SAR 地震前後合成影像，以及光學與 SAR 交集的高信心受損區域分析。
- S8：已將 GeoTIFF export tasks 送出至 Google Drive。
- S10：以挖子尾紅樹林 MNDWI 時間序列完成創意延伸分析。
- S9 與文字回答 cells：已於 notebook 中完成。

## 驗證

已使用 `jupyter nbconvert --execute` 執行 notebook。

主要輸出結果：

- Sentinel-2 images：`143`
- Sentinel-1 GRD images：`146`
- 地震前平均 NDVI spread：`1.0936`
- 地震後平均 NDVI spread：`1.2264`
- 跨感測器高信心受損面積：`0.26 km2`
- 挖子尾 MNDWI 有資料月份數：`65`

GEE export tasks：

| Task | 狀態 |
|---|---|
| `Hualien_DeltaNDVI` | Completed |
| `Hualien_PostEQ_NDVI` | Completed |

匯出的 GeoTIFF 應位於 Google Drive 的 `GEE_Exports/` 資料夾中。

## S10 創意延伸分析

研究區：新北市八里挖子尾紅樹林／河口濕地。

```python
MY_BBOX = [121.413, 25.160, 121.426, 25.171]
my_index_bands = ['B3', 'B11']
my_index_name = 'MNDWI'
MY_START = '2020-01-01'
MY_END = '2026-03-31'
MY_EVENT_DATE = '2024-07-24'
MY_EVENT_LABEL = 'Typhoon Gaemi'
```

註：Sentinel-2 `B11` 為 20 m SWIR 波段。本作業在時間序列 reduction 中使用 `scale=100`，適合作為課堂作業分析尺度；不應將此 MNDWI 時間序列描述為純 10 m 原生解析度產品。

## 備註

- 課程原始 notebook 已保留為 `notebooks/Week13-Student-original.ipynb`，未進行修改。
- 已執行版 notebook 因嵌入地圖元件與圖表輸出，檔案較大。
- 本作業未使用外部 LLM API 或圖像生成 API。
