# Awesome Taiwan Environmental Data [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

**海洋 · 氣候 · 水文 · 能源 · 空品 · 地震 — Ocean · Climate · Hydrology · Energy · Air Quality · Seismic**

> 台灣海洋、氣候、水文、能源、空品、地震開放資料的策展清單，附上實測過的存取方法與踩過的坑。
> A curated list of Taiwan environmental open-data sources spanning ocean, climate,
> hydrology, energy, air quality and seismic, with verified access recipes and the
> gotchas the docs don't tell you.

大多數 awesome-list 只給連結。這份不一樣：每個來源都實際打過，附上能直接跑的 `curl`／Python
片段、時空涵蓋、格式、授權，以及那些文件不會告訴你的雷（氣候平均偽裝成時間序列、鏡像資料
污染、要 URL-encode 的中括號、藏起來的免登入端點、回應最外層是 list、`filters` 參數靜默失效）。

國際上已有全球型的開放資料清單（見 [Related](#related-lists)）；台灣本地也有一些較一般性的
政府資料清單與聚合工具，同樣整理在 Related。據查證，目前尚未有專注於環境、氣候、海洋，並替
每個來源附上實測存取方法與踩坑紀錄的台灣清單，這份補上這個位置，涵蓋 ODB、CWA、台電、環境部、
水利署、漁業署、海保署、TCCIP 等機關。

> [!TIP]
> 這份清單最不一樣的地方在最後兩節。[混淆稽核案例](#worked-examples--混淆稽核案例)把這些資料
> 串成四個真實的「漂亮但錯誤」案例，每一個的 p 值都很漂亮，卻都撐不過稽核；接著是
> [踩過的坑](#gotchas--lessons)。資料很多人給，會騙人的地方很少人講。

> [!NOTE]
> 圖例：✅ 本清單實測可用 · 🔑 需免費註冊或金鑰 · ⚠️ 有雷（見備註）· 📊 網格／NetCDF · 🐟 生物

## Contents
- [海洋 · Ocean (ODB 海洋學門資料庫)](#ocean--odb)
- [海象與氣象 · Marine & Weather (CWA 中央氣象署)](#marine--weather--cwa)
- [歷史觀測 · Historical Observations](#historical-observations)
- [水資源 · Water Resources](#water-resources)
- [能源與電網 · Energy & Grid (台電)](#energy--grid--台電)
- [空氣品質 · Air Quality (環境部)](#air-quality--環境部)
- [地球物理與地震 · Geophysics & Seismic](#geophysics--seismic)
- [財經與產業 · Finance & Industry (TWSE/MOPS)](#finance--industry--twsemops)
- [農業與漁業 · Agriculture & Fisheries](#agriculture--fisheries)
- [海洋保育 · Marine Conservation](#marine-conservation)
- [氣候變遷與災防 · Climate & Disaster](#climate--disaster)
- [衛星與再分析 · Satellite & Reanalysis](#satellite--reanalysis)
- [全球資料（台灣海域可用）· Global (Taiwan-region)](#global-taiwan-region)
- [遙相關因子 · Climate Indices](#climate-indices--teleconnection-factors解釋因子)
- [分析工具 · Tools](#tools)
- [混淆稽核案例 · Worked Examples](#worked-examples--混淆稽核案例)
- [踩過的坑 · Gotchas & Lessons](#gotchas--lessons)
- [Related lists](#related-lists)

---

## Ocean · ODB
[海洋學門資料庫 Ocean Data Bank](https://www.odb.ntu.edu.tw/)（國科會委託台大海研所）。
[Ocean APIverse](https://api.odb.ntu.edu.tw/hub/) 提供多個免金鑰海洋開放 API。
維護者 [@cywhale](https://github.com/cywhale)。

- ✅ **海洋熱浪 MHW API**：全球 0.25° 月尺度 SST、距平、MHW 分級（Hobday 2018）、熱位移。
  `GET https://eco.odb.ntu.edu.tw/api/mhw?lon0=&lat0=&start=&end=&append=sst,sst_anomaly,level`
- ✅ 📊 **OPeNDAP (Hyrax)**：`https://oceanapi.cwa.gov.tw/opendap/hyrax/`，提供 OISST、OCM、HYCOM、
  WW3 網格資料，可用 DAP 約束式只抓子區域。⚠️ OISST 鏡像僅 2018-12 起，且近期月份有 0.0 掉值
  污染（2024-10 高達 39%）與缺月；長序列建議改用 NOAA ERDDAP（見下）。
- ✅ **CTD／SADCP API**：`https://ecodata.odb.ntu.edu.tw/api/ctd`（溫鹽剖面）、`/api/sadcp`（海流
  u／v），逐深度。⚠️ 是氣候平均，不是時間序列（`time_period` 只有 "0"，年份參數被忽略）。
- ✅ **匿名 ticket**：`POST https://ocean.cwa.gov.tw/V2/menu_process/get_api_config` 回一個長效 JWT，
  供免登入呼叫 `oceanapi.cwa.gov.tw/restapi/v2`。
- GEBCO 水深、GLODAP 海洋碳、WOA23、Argo、tide、cruiseAPI，見
  [@cywhale 的 repos](https://github.com/cywhale?tab=repositories)。
- [Hidy Viewer 2](https://odbview.oc.ntu.edu.tw/hidy/)（互動地圖）、
  [MHW 儀表板](https://eco.odb.ntu.edu.tw/pub/MHW/)。
- 📄 [資料政策與引用](https://github.com/oceandatabank/Data-policy)：船測資料引用
  `Ocean Data Bank, NSTC, Taiwan. https://doi.org/10.5281/zenodo.7512112`。

## Marine & Weather · CWA
[交通部中央氣象署](https://www.cwa.gov.tw/)。

- ✅ **CWA 開放資料 S3 bucket（完全公開、可列目錄）**：
  `https://cwaopendata.s3.ap-northeast-1.amazonaws.com/?list-type=2`，5,875 個物件，免金鑰。
  海象四支：`O-B0069`（大潮）、`O-B0070`（長浪）、`O-B0076`（即時海況站表）、`O-B0079`（日均海溫）。
- ✅ **站級海象觀測（免登入）**：
  `POST https://ocean.cwa.gov.tw/V2/map_common/get_station_info`（`station_id`、`kind=tide|buoy`、
  `end_time`），回逐時 SST、海流、波、風、水位，可回溯約 6 年。程式碼裡 `account: 'nologin'`
  是預期用法。
- ✅ **7 天海象預報（免登入）**：`POST .../V2/map_common/get_ocean_data`（`latlng`、`datetime`）。
- 🔑 **歷史海象（2000 起）**：[海象資料下載](https://ocean.cwa.gov.tw/V2/data_interface/datasets)
  `Tide-his`（潮位站含海溫）、`Wave-his`（浮標含風、波、流、水溫），逐年下載，需免費註冊。
- 🔑 **opendata 平台**：`https://opendata.cwa.gov.tw/`，datastore API 需金鑰。氣象、地震、海象
  預報產品皆在此。
- ✅ **海象測站清單**：
  [`station_info.html`](https://oceanapi.cwa.gov.tw/restapi/v2/static/station/station_info.html)，
  55 站（33 潮位 + 14 浮標 + 2 波浪），含起始日（基隆潮位站 1946 起），其中 49 站量海溫。
- [即時海況頁](https://www.cwa.gov.tw/V8/C/M/OBS_Marine.html)、
  [海洋觀測資料](https://climate.cwa.gov.tw/OceanObservation)、
  [ENSO 影響](https://climate.cwa.gov.tw/ENSO)。

## Historical Observations
- ✅ **[Raingel/historical_weather](https://github.com/Raingel/historical_weather)**：台灣歷史氣象
  觀測資料庫，CWA CODIS 陸上測站逐時，GitHub 上機器可讀 CSV（`data/{站碼}/{站碼}_{年}.csv`，
  欄含 `Tx` 氣溫），每日以 GitHub Actions 更新。可補救滾動視窗會掉的資料。
- **[CODiS](https://codis.cwa.gov.tw/)**：CWA 氣候觀測資料查詢（陸上站歷史，含澎湖浮標 SST 2007 起）。
- **[農業氣象觀測網](https://agr.cwa.gov.tw/)**：農業站。⚠️ 只有氣溫、土溫，無水溫。

## Water Resources
[經濟部水利署](https://www.wra.gov.tw/)。
- ✅ **水庫即時水情**：[data.gov.tw/45501](https://data.gov.tw/dataset/45501)，
  `https://opendata.wra.gov.tw/api/v2/2be9044c-6e44-4856-aad5-dd108c2e6679?format=JSON`，
  有效蓄水量、蓄水率、水位，每小時。⚠️ 即時為主，歷史走
  [災防 portal](https://fhy.wra.gov.tw/fhyv2/monitor/reservoir)。
- ⚠️ **河川流量**：`fhy.wra.gov.tw` 首頁在，但直接猜的水情 API 端點會 503；`data.wra.gov.tw`
  一度 DNS 不解析。乾旱、低流量的處理，短期可用 NASA POWER 降水（見
  [衛星與再分析](#satellite--reanalysis)）當代理。

## Energy & Grid · 台電
[台灣電力公司開放資料](https://www.taipower.com.tw/tc/page.aspx?mid=5554)。核心是一組
`apply/file` 檔案：`https://service.taipower.com.tw/data/opendata/apply/file/d0060NN/001.{csv,json}`，
全部免金鑰。

- ✅ **即時各機組發電與裝置容量**：`d006001/001.json`，216 個機組，欄位 `機組類型`、`機組名稱`、
  `裝置容量(MW)`、`淨發電量(MW)`、`淨發電量/裝置容量比(%)`。含 22 個太陽能機組（彰濱光 100 MW、
  南鹽光 150 MW 等）與風力、核能、燃煤、抽蓄。有裝置容量這一點很珍貴。
- ✅ 📊 **十分鐘級各機組淨發電**：`d006010/001.json`，`UNIT_NET_P`，`INTERVAL:10MIN`，約 200 萬筆、
  192 MB。⚠️ 目前只發布 2026 Q1 一檔（`002.json` 起皆 404）。同型的 `d006009` 是 `FLOW_P`（抽蓄、水力）。
- ✅ **每日供需與風力、太陽能**：`d006005/001.csv`（= [data.gov.tw 19995](https://data.gov.tw/dataset/19995)），
  淨尖峰供電能力、尖峰負載、備轉率、風力、太陽能、各機組別，2025 起。⚠️ 是尖峰時點快照，非日總量。
- ✅ **每日備轉容量率**：`d006004/001.csv`（= [data.gov.tw 24945](https://data.gov.tw/dataset/24945)，
  2023 起，序列較長）；近期版為 `d006002`。
- 其他：`d006008` 歲修排程、`d006013` 年尖峰日、`d006015` 逐月系統能力、`d006016` 線損。
- ⚠️ 舊的 `www.taipower.com.tw/d006/loadGraph/.../genary.json`（即時各機組）本清單實測回 0 bytes，
  改用上面的 `apply/file` 系列。

## Air Quality · 環境部
[環境部環境資料開放平臺](https://data.moenv.gov.tw/)（前身環保署）。API 形如
`https://data.moenv.gov.tw/api/v2/{dataset}?api_key={key}&limit=&offset=&format=JSON`。

- ✅ **AQI 歷史（逐時、站級）**：`aqx_p_488`，25 欄，含 `pm10`、`pm10_avg`、`pm2.5`、`pm2.5_avg`、
  `so2`、`co`、`o3`、`no2`、`windspeed`、`winddirec`、`longitude`、`latitude`、`siteid`、
  `datacreationdate`。有風向與座標，可做「下風」空間分析。`sort=datacreationdate` 可回溯到 2016-11。
- ✅ **即時 AQI**：`aqx_p_432`（24 欄，逐時全站）。細懸浮微粒 PM2.5 為 `aqx_p_02`。
- 🔑➡️✅ **拿現行金鑰的正確方法**：舊教學用的公開 key（`9be7b239-…`）已失效（回 500）。現行可用
  金鑰由環境部自己公告在 data.gov.tw 的下載欄位：
  `GET https://data.gov.tw/api/v2/rest/dataset/40448` 的 `result.distribution[].resourceDownloadUrl`
  內含一把可用的 `api_key`（AQI；同一把也開得了 `aqx_p_488`）；PM2.5 見
  [dataset 34827](https://data.gov.tw/api/v2/rest/dataset/34827)。金鑰可能輪替，用這招取即時的；
  長期建議自行到平台免費註冊。
- 📦 **多年份批量**：API 逐頁太慢（見下方雷），批量走
  [airtw 歷史資料](https://airtw.moenv.gov.tw/CHT/Query/His_Data.aspx) 的分區逐年封存
  （2018–2025，Google Drive）。

## Geophysics & Seismic
- ✅ **USGS FDSN 地震目錄**：免金鑰、歷史完整、可框台灣範圍。
  `https://earthquake.usgs.gov/fdsnws/event/1/query?format=geojson&starttime=&endtime=&minlatitude=21&maxlatitude=26&minlongitude=119&maxlongitude=123&minmagnitude=4`
  （`format=csv|text|quakeml` 亦可）。本清單實測台灣範圍 2014–2026 M ≥ 4.0 有 2,044 筆。
- 🔑 **CWA 地震目錄**：[opendata.cwa.gov.tw](https://opendata.cwa.gov.tw/) 有顯著、小區域有感地震
  報告，但偏近期報告且需金鑰。要長歷史、免金鑰就用 USGS。
- [中央氣象署地震測報中心](https://scweb.cwa.gov.tw/)（人工查詢介面）。

## Finance & Industry · TWSE/MOPS
把產業、經濟接進環境分析（例如地震 × 晶圓廠、氣候 × 農產）時的公開來源。

- ✅ **每日個股股價**：證交所
  `https://www.twse.com.tw/exchangeReport/STOCK_DAY?response=json&date=YYYYMM01&stockNo=2330`，
  免金鑰，一次一個月，含開高低收與量。⚠️ 日期是民國年（`113/04/01`）。
- ✅ **上市公司月營收（歷史）**：公開資訊觀測站靜態頁
  `https://mopsov.twse.com.tw/nas/t21/sii/t21sc03_{民國年}_{月}.html`（`sii` 為上市，`otc` 為上櫃）。
  ⚠️ Big5 編碼；⚠️ 要用 `mopsov`.twse.com.tw，`mops.twse.com.tw/nas/...` 那條會 404。
- ✅ **TWSE OpenAPI**：`https://openapi.twse.com.tw/v1/opendata/t187ap05_L`（月營收，含產業別）。
  ⚠️ 只給當月快照，要歷史序列就用上面的 mopsov 靜態頁逐月抓。

## Agriculture & Fisheries
[農業部](https://www.moa.gov.tw/)、[漁業署](https://www.fa.gov.tw/)。
- ✅ **農業部開放資料（正確用法）**：不是 `/api/...`（那些會 500 或回 HTML），而是
  `https://data.moa.gov.tw/Service/OpenData/DataFileService.aspx?UnitId={id}&FOTT=Json&IsTransData=1`
  （`FOTT=CSV|Xml|Json`）。漁業署走 `FaRss.aspx?key={k}&UnitId={id}&FOTT=Json&IsTransData=1`。
- **[漁業統計年報](https://www.fa.gov.tw/view.php?theme=FS_AR)**：產量、貿易、設施（無災害章節）。
- ⚠️ **漁業天然災害災損**：事件級（鄉鎮 × 魚種 × 死因 × 金額）只在需登入的
  [`disaster.fa.gov.tw`](https://disaster.fa.gov.tw/)；開放資料只有「單一事件 × 縣 × 部門」的聚合。

## Marine Conservation
- **[海洋保育網 iOcean](https://iocean.oca.gov.tw/)**（海保署）：珊瑚健康公民回報、海洋資料匯流，
  含 CWA 即時海氣象水文鏡像。部分下載需申請。
- **[珊瑚礁體檢 / Reef Check Taiwan](https://coral.relab.cc/)**、台灣珊瑚礁學會。⚠️ 多為 PDF 或
  公民回報，未與 SST 連結。

## Climate & Disaster
- **[TCCIP 氣候變遷推估資訊](https://tccip.ncdr.nat.gov.tw/)**（NCDR）：AR6 降尺度推估、養殖漁業
  危害指標（虱目魚、文蛤、吳郭魚、石斑的冷熱危害）、災害衝擊。
- **NOAA Niño3.4 / ONI**：`https://psl.noaa.gov/data/timeseries/month/data/nino34.long.anom.data`
  （ENSO，1870 起）。

## Satellite & Reanalysis
- ✅ 📊 **NASA POWER（逐日、逐點、免金鑰）**：太陽輻照、氣膠、氣象一站取得，是做能源 × 空品
  分析的主力。
  `https://power.larc.nasa.gov/api/temporal/daily/point?parameters=ALLSKY_SFC_SW_DWN,CLRSKY_SFC_SW_DWN,CLOUD_AMT,AOD_55,WS10M,T2M,PRECTOTCORR&community=RE&longitude=&latitude=&start=YYYYMMDD&end=YYYYMMDD&format=JSON`
  （全天空、晴空日射量、雲量、550nm 氣膠 AOD、風速、氣溫、降水）。⚠️ 缺值以 -999 表示；⚠️
  `AOD_55` 是 MERRA-2（約 0.5° 粗網格），相距 110 km 的兩點可能回完全相同的序列；⚠️
  `ALLSKY_SFC_SW_DWN` 是用 AOD 模擬算出來的，別拿它回歸 AOD（循環論證，見
  [混淆稽核案例](#worked-examples--混淆稽核案例)）。
- **[TASA 國家太空中心](https://www.tasa.org.tw/)**：FORMOSAT 系列、Triton（2023，GNSS 反射估海面
  風）。⚠️ 以任務為主，無明顯海洋產品開放 API。

## Global (Taiwan-region)
台灣海域可用的全球開放資料。
- ✅ 📊 **NOAA OISST v2.1（逐日 0.25°，1981 起）via ERDDAP**：打台灣事件的主力，比 ODB 鏡像
  乾淨、更長。
  `https://coastwatch.pfeg.noaa.gov/erddap/griddap/ncdcOisst21Agg_LonPM180.csv?sst[(t0):(t1)][(0.0)][(lat0):(lat1)][(lon0):(lon1)]`
  ⚠️ 中括號要 URL-encode 成 `%5B%5D`；單次請求 9 年為上限，要分塊。
- 📊 **NOAA PSL OPeNDAP**：`psl.noaa.gov/thredds/.../noaa.oisst.v2.highres/`。⚠️ 併發時常 DAP
  failure，建議改 ERDDAP。
- **[NOAA Coral Reef Watch](https://coralreefwatch.noaa.gov/)**：全球 5 km DHW、白化警報，台灣有
  Southern Taiwan、Dongsha 兩個虛擬站。
- **[Copernicus Marine](https://data.marine.copernicus.eu/)**：GLORYS12V1 全球再分析（1/12°，
  溫鹽流、海平面，1993 起）。
- **[NASA Earthdata / PODAAC](https://www.earthdata.nasa.gov/topics/ocean)**、
  [Ocean Color](https://oceandata.sci.gsfc.nasa.gov/)。

## Climate Indices & Teleconnection Factors（解釋因子）
把台灣的海溫、極端事件放進驅動因子的脈絡，讓分析更可解釋、增加實驗變數。
> [!WARNING]
> 這些因子要實測，別假設。本清單實測：DJF ENSO（ONI）對台灣近海冬季海溫距平的相關只有
> r = −0.03；打台灣最兇的西北太平洋 MHW 有研究指與 ENSO 無關（繞極波列驅動）。台灣海峽近海由
> 季風與沿岸流主導，不是赤道遙相關，這正是[混淆稽核案例](#worked-examples--混淆稽核案例)
> 反覆出現的教訓。
- ✅ **ENSO**：Niño3.4 月距平
  `https://psl.noaa.gov/data/timeseries/month/data/nino34.long.anom.data`（1870 起）；
  [ONI (CPC)](https://www.cpc.ncep.noaa.gov/products/analysis_monitoring/ensostuff/ONI_v5.php)、
  [MEI v2](https://psl.noaa.gov/enso/mei/)、SOI。
- **PDO 太平洋年代際振盪**：[NOAA/NCEI PDO](https://www.ncei.noaa.gov/access/monitoring/pdo/)
  （台灣海峽冬季冷事件對應 La Niña 加負 PDO）。
- **IOD 印度洋偶極 (DMI)**：[NOAA PSL DMI](https://psl.noaa.gov/gcos_wgsp/Timeseries/DMI/)。
- **MJO 季內振盪**：[CPC RMM index](https://www.cpc.ncep.noaa.gov/products/precip/CWlink/MJO/mjo.shtml)。
- **NAO / AO**：[CPC](https://www.cpc.ncep.noaa.gov/products/precip/CWlink/daily_ao_index/ao.shtml)。
- **黑潮 Kuroshio**：流量、入侵可由衛星測高
  [AVISO/Copernicus](https://data.marine.copernicus.eu/) 導出。
- 📊 **ERA5 全球再分析**：[Copernicus CDS](https://cds.climate.copernicus.eu/)（大氣因子：氣溫、
  風、氣壓、熱通量）。

## Tools
- **[ecjoliver/marineHeatWaves](https://github.com/ecjoliver/marineHeatWaves)**（Python，Hobday
  MHW／MCS 參考實作）。⚠️ numpy 2.x 要小補（`np.NaN` 改 `np.nan`、單元素布林索引加 `[0]`）。
- **[heatwaveR](https://robwschlegel.github.io/heatwaveR/)**（R，C++ 優化，含逐格偵測 vignette）。
- **[NODASS 2026 教材](https://github.com/ODBapp/2026_NODASS_workshop)**：ODB 海洋資料分析範例
  （geo、bio、mhw、timeseries）。

## Worked Examples · 混淆稽核案例
這節是本清單的靈魂。用上面這些資料做了四個跨領域題目，四個天真估計都很漂亮，四個都是假的。
每個 repo 都把會騙人的那一步留在程式碼裡可重跑，並附一份共用的
[混淆稽核清單 CONFOUND-AUDIT.md](https://github.com/thc1006/taiwan-earthquake-fab/blob/main/CONFOUND-AUDIT.md)。

| 題目（用到的來源） | 天真結果 | 死因 |
|---|---|---|
| [taiwan-solar-dimming](https://github.com/thc1006/taiwan-solar-dimming)（NASA POWER + 台電光電） | 氣膠 β = −85，p < 0.0001 | 季節同步（冬春氣膠高、發電效率也剛好低） |
| [taiwan-earthquake-fab](https://github.com/thc1006/taiwan-earthquake-fab)（USGS + TWSE/MOPS） | 點估計為負且合理 | 檢定力不足（月營收 MDE ≈ 11 pp，實際衝擊 1.3%） |
| [taiwan-riverbed-dust](https://github.com/thc1006/taiwan-riverbed-dust)（環境部 aqx_p_488） | 下風 +0.65，p = 0.0001 | 順流向空間梯度（陰性對照與位移安慰劑戳破） |
| [taiwan-vre-drought](https://github.com/thc1006/taiwan-vre-drought)（台電供需） | 相關 r = +0.51 | 會計恆等式（再生能源本就是供電能力的組成） |

四題全部免金鑰、可重跑。它們同時也是這份清單各來源的壓力測試：每個 API 都被實際拿去做過分析。

## Gotchas & Lessons
這份清單最值錢的部分，是文件不會告訴你的雷（皆本清單實測）。

**海洋、氣候**
- ODB OISST OPeNDAP 鏡像有 0.0 掉值污染（近期月份達 39%）、缺月，且僅 2018 年起；長序列改用
  NOAA ERDDAP。
- ODB 的 CTD／SADCP 是氣候平均，不是時間序列，錨不了特定事件。
- 固定基期的 Hobday MHW／MCS 偵測在暖化下會壞：冷側事件消失、熱側飽和；改用隨趨勢移動的基期。
- NOAA PSL OPeNDAP 併發時容易 DAP failure；改走 ERDDAP 分塊（9 年以內、括號 URL-encode）較穩。
- 潮位站海溫是港內 0–5 m 的水溫，不代表外海。

**空品、能源、財經（新）**
- 環境部 API 回應的最外層是 list，不是 `{"records":[…]}`，直接當陣列處理。
- 環境部 `filters` 參數對日期靜默失效（給了 2021 卻回當日資料）；歷史用 `offset` 分頁（offset
  對日期近乎線性，約每月 6 ~ 7 萬筆），多年份改走 airtw 分年封存。
- 環境部舊公開金鑰已失效（回 500）；現行金鑰在 data.gov.tw 的 `resourceDownloadUrl` 欄位裡。
- 台電十分鐘級發電只發布了 2026 Q1 一檔（`002.json` 起皆 404）；舊的 `genary.json` 回 0 bytes。
- 月營收要用 `mopsov`.twse.com.tw（`mops.` 會 404）；TWSE OpenAPI 只給當月快照；日期是民國年。
- NASA POWER 的 `ALLSKY` 是用 AOD 算出來的，別拿它回歸 AOD（循環）；`AOD_55` 網格粗，相鄰兩點
  可能同值。
- `data.cdc.gov.tw` 有 TLS 憑證錯誤；`data.gov.tw` 搜尋頁是 JS shell，改用 `api/v2/rest/dataset/{id}`
  以 ID 取。

**方法**
- 農業部開放資料用 `DataFileService.aspx?FOTT=Json`，別打 `/api/...`（會 500 或回 HTML）。
- CWA 海象歷史（2000 起）要免費註冊；事件級漁業災損需登入。
- 相關性常是 misinterpretation，見上一節四個案例。宣稱發現前，先過
  [CONFOUND-AUDIT.md](https://github.com/thc1006/taiwan-earthquake-fab/blob/main/CONFOUND-AUDIT.md)。

## Related lists

**全球 / 國際**
- [pangeo-data/awesome-open-climate-science](https://github.com/pangeo-data/awesome-open-climate-science)
- [Inria-Chile/awesome-ocean-ai-data](https://github.com/Inria-Chile/awesome-ocean-ai-data)
- [monocilindro/Awesome-Hydrospatial](https://github.com/monocilindro/Awesome-Hydrospatial)
- [KKulma/climate-change-data](https://github.com/KKulma/climate-change-data)

**台灣（範圍不同或較一般）**，這幾份和本清單互補，不重疊：
- [onlinemad/awesome-opendata-taiwan-gov](https://github.com/onlinemad/awesome-opendata-taiwan-gov)：台灣政府開放資料與 API，泛用取向
- [lun916/awesome-taiwan-business-data](https://github.com/lun916/awesome-taiwan-business-data)：公司、商業登記、政府標案、法律案件
- [mosdeo/List_of_Taiwan_OpenData_Diamonds_and_Rust](https://github.com/mosdeo/List_of_Taiwan_OpenData_Diamonds_and_Rust)：政府開放資料的踩雷筆記
- [cm-ailab-cc-plugins/plugin-twinkle-hub](https://github.com/cm-ailab-cc-plugins/plugin-twinkle-hub)：台灣開放資料的 MCP 聚合端點（52,960+ 資料集）

## Contributing
歡迎 PR，做法見 [CONTRIBUTING.md](CONTRIBUTING.md)。原則很簡單：每個來源要實際測過，附上存取
方法與（若有）雷，並遵循 [awesome 準則](https://github.com/sindresorhus/awesome/blob/main/awesome.md)。
參與請一併遵守[行為準則](CODE_OF_CONDUCT.md)。

## License
[![CC0](https://licensebuttons.net/p/zero/1.0/80x15.png)](https://creativecommons.org/publicdomain/zero/1.0/)
清單內容以 [CC0](LICENSE) 釋出。各資料源的著作權與授權，依其原始規定。
