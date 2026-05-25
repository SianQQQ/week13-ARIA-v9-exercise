# 第 13 週作業 — ARIA v9.0 Cloud Engine

國立臺灣大學遙測與空間資訊分析  
第 13 週：Google Earth Engine 與雲端尺度時間序列分析

## 檔案

| 路徑 | 說明 |
|---|---|
| `notebooks/Week13_ARIA_v90_張育憲_Completed_executed.ipynb` | 主要繳交 notebook，已執行所有 cells 並保留輸出結果 |
| `notebooks/Week13_ARIA_v90_張育憲_Completed.ipynb` | 完成版原始 notebook，已填入所有空格與文字回答 |
| `exports/taroko_post_eq_ndvi.tif` | S8 匯出的秀林／太魯閣震後 NDVI GeoTIFF |
| `exports/taroko_delta_ndvi.tif` | S8 匯出的秀林／太魯閣 Delta-NDVI GeoTIFF |
| `exports/taroko_summary.json` | 主要統計數值摘要 |
| `reports/integration_summary.md` | Task 4 integration summary report |
| `course_materials/Week13-Homework-ARIA-v9.md` | 作業規格 |
| `course_materials/Pre-lab-Week13.md` | 課前檢查表與概念複習 |
| `course_materials/Week13-Slides.pptx` | 第 13 週課程投影片 |

## 完成內容

- S1：初始化 GEE 環境，使用 project `white-gate-489512-r8`。
- S2-S3：完成 Sentinel-2 影像集合篩選、SCL 遮罩與 NDVI 計算。
- S4-S4b：建立秀林／太魯閣每月 NDVI mean/min/max 時間序列、變異幅度圖，並與花蓮市 demo AOI 比較尺度差異。
- S5：完成三個時期的 NDVI 中位數合成影像與 Delta-NDVI 變化圖。
- S6-S7：完成 Sentinel-1 VV 時間序列、SAR 地震前後合成影像，以及光學與 SAR 交集的高信心受損區域分析。
- S8：已將 GeoTIFF export tasks 送出並把完成檔放入本 repo 的 `exports/`。
- S9 與文字回答 cells：已於 notebook 中完成。

## 驗證

已使用 `jupyter nbconvert --execute` 執行 notebook。

主要輸出結果：

- Sentinel-2 images：`291`
- Sentinel-1 GRD images：`146`
- 地震前平均 NDVI spread：`1.5926`
- 地震後平均 NDVI spread：`1.6528`
- 跨感測器高信心受損面積：`1.59 km2`

GeoTIFF 成果檔：

| 檔案 | 說明 |
|---|---|
| `exports/taroko_delta_ndvi.tif` | Xiulin / Taroko Delta-NDVI map |
| `exports/taroko_post_eq_ndvi.tif` | Xiulin / Taroko post-earthquake NDVI composite |

## 備註

- S10 創意延伸分析已移至獨立期末 repo，不放在本週作業繳交包中。
- 已執行版 notebook 因嵌入地圖元件與圖表輸出，檔案較大。
- 本作業未使用外部 LLM API 或圖像生成 API。
