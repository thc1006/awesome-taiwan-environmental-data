# 存取細節與踩坑 · Access Recipes & Gotchas

[README](README.md) 各來源的完整版：實際端點、參數、時空涵蓋，以及文件不會告訴你的雷。
所有內容本清單都實際打過。內文的「注意」就是踩過的坑。

## Ocean · ODB

**海洋熱浪 MHW API**
`GET https://eco.odb.ntu.edu.tw/api/mhw?lon0=&lat0=&start=&end=&append=sst,sst_anomaly,level`
全球 0.25° 月尺度 SST、距平、MHW 分級（Hobday 2018）、熱位移。

**OPeNDAP (Hyrax)**
`https://oceanapi.cwa.gov.tw/opendap/hyrax/` — OISST、OCM、HYCOM、WW3 網格，可用 DAP 約束式只抓子區域。
注意：OISST 鏡像僅 2018-12 起，近期月份有 0.0 掉值污染（2024-10 高達 39%）與缺月；長序列改用 NOAA ERDDAP。

**CTD／SADCP API**
`https://ecodata.odb.ntu.edu.tw/api/ctd`（溫鹽剖面）、`/api/sadcp`（海流 u／v），逐深度。
注意：是氣候平均，不是時間序列（`time_period` 只有 "0"，年份參數被忽略）。

**匿名 ticket**
`POST https://ocean.cwa.gov.tw/V2/menu_process/get_api_config` 回一個長效 JWT，供免登入呼叫 `oceanapi.cwa.gov.tw/restapi/v2`。
注意：這是未文件化的網頁內部端點，不是官方公開、受版本管理的 API，可能隨前端改版失效；不要把 token commit 進版本庫。

引用規範：船測資料引用 `Ocean Data Bank, NSTC, Taiwan. https://doi.org/10.5281/zenodo.7512112`（見 [資料政策](https://github.com/oceandatabank/Data-policy)）。

## Marine & Weather · CWA

**S3 bucket（完全公開、可列目錄）**
`https://cwaopendata.s3.ap-northeast-1.amazonaws.com/?list-type=2`，免金鑰。
海象四支：`O-B0069`（大潮）、`O-B0070`（長浪）、`O-B0076`（即時海況站表）、`O-B0079`（日均海溫）。
注意：`ListObjectsV2` 單次最多回 1,000 個物件，要列完整個 bucket（實測約數千個）得用 `continuation-token` 分頁。

**站級海象觀測（免登入）**
`POST https://ocean.cwa.gov.tw/V2/map_common/get_station_info`（`station_id`、`kind=tide|buoy`、`end_time`），回逐時 SST、海流、波、風、水位，可回溯約 6 年。程式碼裡 `account: 'nologin'` 是預期用法。

**7 天海象預報（免登入）**
`POST .../V2/map_common/get_ocean_data`（`latlng`、`datetime`）。

**歷史海象（2000 起）**
[海象資料下載](https://ocean.cwa.gov.tw/V2/data_interface/datasets)：`Tide-his`（潮位站含海溫）、`Wave-his`（浮標含風、波、流、水溫），逐年下載，需免費註冊。

**海象測站清單**
[`station_info.html`](https://oceanapi.cwa.gov.tw/restapi/v2/static/station/station_info.html)：分潮位站、資料浮標、波浪站三類，多數站含海溫欄位。
注意：站表含已停測站，以「資料結束日期」欄區分目前運作與歷史站；站數會變動，別把某個當下的總數寫死。

## Water Resources

**水庫即時水情**（[data.gov.tw/45501](https://data.gov.tw/dataset/45501)）
`https://opendata.wra.gov.tw/api/v2/2be9044c-6e44-4856-aad5-dd108c2e6679?format=JSON` — 有效蓄水量、蓄水率、水位，每小時。歷史走 [災防 portal](https://fhy.wra.gov.tw/fhyv2/monitor/reservoir)。
注意：河川流量目前沒有穩定的免金鑰端點（`fhy.wra.gov.tw` 水情 API 會 503、`data.wra.gov.tw` 一度 DNS 不解析）。在拿到實測流量前，NASA POWER 降水只能當氣象乾旱的粗略 covariate，不是流量代理（流量還受集水面積、前期土壤含水、蒸發散、水庫調度與 lag 影響）。

## Energy & Grid · 台電

核心是一組 `apply/file` 檔案：`https://service.taipower.com.tw/data/opendata/apply/file/d0060NN/001.{csv,json}`，全部免金鑰。

- **`d006001/001.json`**：即時各機組發電與裝置容量，216 個機組，欄位 `機組類型`、`機組名稱`、`裝置容量(MW)`、`淨發電量(MW)`。含 22 個太陽能機組（彰濱光 100 MW、南鹽光 150 MW 等）與風力、核能、燃煤、抽蓄。有裝置容量這點很珍貴。
- **`d006010/001.json`**：十分鐘級各機組淨發電（`UNIT_NET_P`、`INTERVAL:10MIN`，約 200 萬筆、192 MB）。注意：目前只發布 2026 Q1 一檔（`002.json` 起皆 404）。同型 `d006009` 是 `FLOW_P`（抽蓄、水力）。
- **`d006005/001.csv`**（= [19995](https://data.gov.tw/dataset/19995)）：每日供需與風力、太陽能，2025 起。注意：風力／太陽能欄位是萬瓩（功率），是尖峰時點的瞬時出力快照，不是全日發電量。
- **`d006004/001.csv`**（= [24945](https://data.gov.tw/dataset/24945)）：每日備轉率，2023 起。其他：`d006008` 歲修、`d006013` 年尖峰日、`d006015` 逐月系統能力、`d006016` 線損。
- 注意：舊的 `www.taipower.com.tw/d006/loadGraph/.../genary.json` 實測回 0 bytes，改用上面的 `apply/file` 系列。

## Air Quality · 環境部

API 形如 `https://data.moenv.gov.tw/api/v2/{dataset}?api_key={key}&limit=&offset=&format=JSON`。

- **`aqx_p_488`**（AQI 歷史，逐時、站級，25 欄）：`pm10`、`pm10_avg`、`pm2.5`、`pm2.5_avg`、`so2`、`co`、`o3`、`no2`、`windspeed`、`winddirec`、`longitude`、`latitude`、`siteid`、`datacreationdate`。`sort=datacreationdate` 可回溯到 2016-11。
- **`aqx_p_432`**（即時 AQI）、**`aqx_p_02`**（PM2.5）。
- **金鑰**：正式做法是到平台免費註冊自己的 key。急用時的非正式 fallback：環境部在 data.gov.tw 的下載欄位公告了可用 key，`GET https://data.gov.tw/api/v2/rest/dataset/40448` 的 `result.distribution[].resourceDownloadUrl` 內含一把（AQI，同一把也開得了 `aqx_p_488`），PM2.5 見 [dataset 34827](https://data.gov.tw/api/v2/rest/dataset/34827)。這把是公用、可能輪替、不保證持續可用。
- 注意：伺服器端日期篩選不可靠 — `filters` 與官方文件所列的 `year_month=YYYY_MM` 都回當日資料、不回指定月份（2026-07-28 實測）。歷史用 `offset` 分頁（offset 對日期近乎線性，約每月 6～7 萬筆），多年份改走 [airtw 分區逐年封存](https://airtw.moenv.gov.tw/CHT/Query/His_Data.aspx)（2018–2025）。回應最外層是 list，不是 `{"records":[…]}`。

## Geophysics & Seismic

**USGS FDSN 地震目錄**（免金鑰、歷史完整）
`https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=&endtime=&minlatitude=21&maxlatitude=26&minlongitude=119&maxlongitude=123&minmagnitude=4`（`format=csv|text|quakeml` 亦可）。本清單實測台灣範圍 2014–2026 M ≥ 4.0 有 2,044 筆。
注意：CWA 地震目錄偏近期報告且需金鑰，要長歷史、免金鑰就用 USGS。

## Finance & Industry · TWSE/MOPS

**TWSE 每日個股股價**
`https://www.twse.com.tw/exchangeReport/STOCK_DAY?response=json&date=YYYYMM01&stockNo=2330`，免金鑰，一次一個月。
注意：請求參數 `date` 用西元 `YYYYMMDD`（例 `20260401`），但回傳資料裡的交易日期是民國年（例 `115/04/01`），兩者別搞混。

**MOPS 上市公司月營收（歷史）**
`https://mopsov.twse.com.tw/nas/t21/sii/t21sc03_{民國年}_{月}.html`（`sii` 上市、`otc` 上櫃），Big5 編碼。
注意：要用 `mopsov`.twse.com.tw，`mops.twse.com.tw/nas/...` 那條會 404。TWSE OpenAPI `t187ap05_L` 只給當月快照。

## Agriculture & Fisheries

**農業部開放資料**（正確用法）
`https://data.moa.gov.tw/Service/OpenData/DataFileService.aspx?UnitId={id}&FOTT=Json&IsTransData=1`（`FOTT=CSV|Xml|Json`）。漁業署走 `FaRss.aspx?key={k}&UnitId={id}&FOTT=Json&IsTransData=1`。
注意：別打 `/api/...`，會 500 或回 HTML。

**漁業災損**：事件級（鄉鎮 × 魚種 × 死因 × 金額）只在需登入的 [`disaster.fa.gov.tw`](https://disaster.fa.gov.tw/)；開放資料只有「單一事件 × 縣 × 部門」的聚合。

## Satellite & Reanalysis

**NASA POWER**（逐日、逐點、免金鑰）
`https://power.larc.nasa.gov/api/temporal/daily/point?parameters=ALLSKY_SFC_SW_DWN,CLRSKY_SFC_SW_DWN,CLOUD_AMT,AOD_55,WS10M,T2M,PRECTOTCORR&community=RE&longitude=&latitude=&start=YYYYMMDD&end=YYYYMMDD&format=JSON`
注意：缺值以 -999 表示；Daily API 時間基準預設是 LST（有 `time-standard=UTC|LST`），跨台電「日」合併要對齊；`AOD_55` 是 MERRA-2（約 0.5° 粗網格），相距 110 km 的兩點可能回完全相同的序列；`ALLSKY_SFC_SW_DWN` 主要源自 CERES SYN1deg（其輻射傳輸含氣膠輸入），與 AOD 並非獨立量測，別拿它當驗證氣膠效應的獨立結果變數。

## Global (Taiwan-region)

**NOAA OISST v2.1 via ERDDAP**（逐日 0.25°，1981 起）
`https://coastwatch.pfeg.noaa.gov/erddap/griddap/ncdcOisst21Agg_LonPM180.csv?sst[(t0):(t1)][(0.0)][(lat0):(lat1)][(lon0):(lon1)]`
注意：中括號要 URL-encode 成 `%5B%5D`；單次請求 9 年為上限，要分塊。比 ODB 鏡像乾淨、更長。

**其他**：[NOAA PSL OPeNDAP](https://psl.noaa.gov/)（併發時常 DAP failure，建議改 ERDDAP）、[Coral Reef Watch](https://coralreefwatch.noaa.gov/)（全球 5 km DHW、白化警報）、[Copernicus Marine](https://data.marine.copernicus.eu/)（GLORYS12V1，1993 起）、[NASA Earthdata](https://www.earthdata.nasa.gov/topics/ocean)。

## Climate Indices

- **ENSO**：Niño3.4 月距平 `https://psl.noaa.gov/data/timeseries/month/data/nino34.long.anom.data`（1870 起）；[ONI](https://www.cpc.ncep.noaa.gov/products/analysis_monitoring/ensostuff/ONI_v5.php)、[MEI](https://psl.noaa.gov/enso/mei/)、SOI。
- **PDO**（[NOAA/NCEI](https://www.ncei.noaa.gov/access/monitoring/pdo/)）、**IOD/DMI**（[NOAA PSL](https://psl.noaa.gov/gcos_wgsp/Timeseries/DMI/)）、**MJO**（[CPC RMM](https://www.cpc.ncep.noaa.gov/products/precip/CWlink/MJO/mjo.shtml)）、**NAO/AO**（[CPC](https://www.cpc.ncep.noaa.gov/products/precip/CWlink/daily_ao_index/ao.shtml)）、**黑潮**（衛星測高導出）、**ERA5**（[Copernicus CDS](https://cds.climate.copernicus.eu/)）。
- 注意：這些因子要實測、別假設。本清單實測 DJF ENSO(ONI) 對台灣近海冬季海溫距平的零延遲線性相關只有 −0.03；海域範圍、期間與去趨勢設定會影響數值（各案例 repo 有可重現版本）。

## Tools

- [ecjoliver/marineHeatWaves](https://github.com/ecjoliver/marineHeatWaves)（Python，Hobday MHW／MCS 參考實作）。注意：numpy 2.x 要小補（`np.NaN` 改 `np.nan`、單元素布林索引加 `[0]`）。
- [heatwaveR](https://robwschlegel.github.io/heatwaveR/)（R，含逐格偵測）。

## 跨來源的雷

- **相關性常被誤讀**：宣稱發現前，先過各案例 repo 的 [CONFOUND-AUDIT.md](https://github.com/thc1006/taiwan-earthquake-fab/blob/main/CONFOUND-AUDIT.md)。
- **固定 vs 移動 MHW 基期回答不同問題**：固定基期（Hobday 原定義）給跨期、跨區可比的長期暴露，暖化下會趨於飽和；移動或去趨勢基期看相對當時氣候的異常。依問題選，必要時兩種都報。
- `data.cdc.gov.tw` 有 TLS 憑證錯誤；`data.gov.tw` 搜尋頁是 JS shell，改用 `api/v2/rest/dataset/{id}` 以 ID 取。
- 農業部開放資料用 `DataFileService.aspx?FOTT=Json`，別打 `/api/...`。
