# Week 13 Integration Summary

本次 ARIA v9.0 作業把前幾週的單景分析升級為 Google Earth Engine 雲端時序分析。Sentinel-2 篩選後共處理 143 景影像，Sentinel-1 GRD 共 146 景；若每景 Sentinel-2 約 800 MB，僅下載 S2 就約 114 GB，以 50 Mbps 連線估計約需 5.1 小時，且尚未包含本機前處理與雲遮罩時間。GEE 的優勢在於影像集合篩選、雲端 reducer、median composite 與 GeoTIFF 匯出都在伺服器端完成。

NDVI 時序顯示花蓮研究區具有季節變化，震後平均 NDVI spread 從 1.0936 增加到 1.2264，代表空間異質性變強。三期 composite 的 ΔNDVI 分析中，地震階段 ΔNDVI < -0.15 面積約 4.12 km2；堰塞湖後續變化階段約 40.17 km2；總累積變化約 32.71 km2。這表示 median composite 比 W9 的兩景差異更穩健，能降低單一雲影、照度或物候異常造成的誤判，也能拆分不同災害事件的貢獻。

Sentinel-1 VV 時序延伸了 W10 的單景 SAR 分析，提供不受雲量限制的地表粗糙度變化資訊。光學與 SAR 交叉比對後，ΔNDVI < -0.15 且 |ΔVV| > 2 dB 的高信心受損區域約 0.26 km2；這些區域同時具有植被損失與雷達後向散射改變，可信度高於單一感測器判斷。若將匯出的 `hualien_delta_ndvi.tif` 放入 W12 Random Forest 分類流程，預期可提升地震受損分類，因為它加入了事件前後變化量，而不只是震後單一影像狀態。

限制方面，GEE 不適合任意 Python 套件、細緻模型除錯、客製化機器學習 pipeline，或未上架 GEE catalog 的資料；InSAR SLC 這類專門流程仍需本機或專用平台。因此，W8-W12 的本機方法仍適合演算法控制、訓練資料設計與分類模型實作；GEE 則適合作為大尺度、多時期資料準備與快速監測平台。
