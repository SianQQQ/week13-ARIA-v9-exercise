# Week 13 Exercise — ARIA v9.0 Cloud Engine

NTU Remote Sensing & Spatial Information Analysis  
Week 13: Google Earth Engine & Cloud-Scale Time Series

## Files

| Path | Purpose |
|---|---|
| `notebooks/Week13_ARIA_v90_張育憲_Completed_executed.ipynb` | Main submission notebook with all cells executed and outputs embedded |
| `notebooks/Week13_ARIA_v90_張育憲_Completed.ipynb` | Clean completed source notebook with all blanks and written answers filled |
| `notebooks/Week13-Student-original.ipynb` | Original student exercise notebook |
| `course_materials/Pre-lab-Week13.md` | Pre-lab checklist and concept review |
| `course_materials/Week13-Slides.pptx` | Week 13 lecture slides |

## What Was Completed

- S1: GEE environment initialized with project `white-gate-489512-r8`.
- S2-S3: Sentinel-2 collection filtering, SCL masking, and NDVI calculation.
- S4-S4b: Monthly NDVI mean/min/max time series, spread plots, and broad Hualien vs Taroko scale comparison.
- S5: Three-phase NDVI median composites and Delta-NDVI maps.
- S6-S7: Sentinel-1 VV time series, SAR pre/post composites, and optical-SAR high-confidence damage intersection.
- S8: GeoTIFF export tasks submitted to Google Drive.
- S10: Creative exploration using Wazihwei mangrove MNDWI time series.
- S9 and written-response cells: completed in the notebook.

## Verification

Executed with `jupyter nbconvert --execute`.

Key outputs:

- Sentinel-2 images: `143`
- Sentinel-1 GRD images: `146`
- Pre-earthquake average NDVI spread: `1.0936`
- Post-earthquake average NDVI spread: `1.2264`
- Cross-sensor high-confidence damage area: `0.26 km2`
- Wazihwei MNDWI months with data: `65`

GEE export tasks:

| Task | Status |
|---|---|
| `Hualien_DeltaNDVI` | Completed |
| `Hualien_PostEQ_NDVI` | Completed |

The exported GeoTIFFs should appear in Google Drive under `GEE_Exports/`.

## S10 Creative Exploration

Study area: Wazihwei mangrove / estuarine wetland, Bali, New Taipei.

```python
MY_BBOX = [121.413, 25.160, 121.426, 25.171]
my_index_bands = ['B3', 'B11']
my_index_name = 'MNDWI'
MY_START = '2020-01-01'
MY_END = '2026-03-31'
MY_EVENT_DATE = '2024-07-24'
MY_EVENT_LABEL = 'Typhoon Gaemi'
```

Note: Sentinel-2 `B11` is a 20 m SWIR band. This exercise uses `scale=100` for time-series reduction, which is appropriate for the class exercise. Do not describe the MNDWI time series as a pure 10 m native-resolution product.

## Notes

- The original course notebook is preserved unchanged as `notebooks/Week13-Student-original.ipynb`.
- The executed notebook is larger because it embeds map widgets and plot outputs.
- No external LLM API or image generation API was used.
