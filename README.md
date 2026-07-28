# Awesome Taiwan Environmental Data [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

**海洋 · 氣候 · 水文 · 能源 · 空品 · 地震 — Ocean · Climate · Hydrology · Energy · Air Quality · Seismic**

> 台灣環境開放資料的策展清單，每個來源都實際打過。這頁只放來源與一句話重點；
> 完整的存取端點、參數與踩坑整理在 **[RECIPES.md](RECIPES.md)**。
> A curated list of Taiwan environmental open-data sources — one line each here;
> the endpoints, parameters and gotchas live in **[RECIPES.md](RECIPES.md)**.

台灣本地已有幾份較一般性的政府資料清單（見 [Related](#related-lists)），但少有專注在環境、
氣候、海洋，並替每個來源附上實測存取方法的。這份補上這個位置，涵蓋 ODB、CWA、台電、環境部、
水利署、漁業署、海保署、TCCIP 等機關。

## Contents
- [海洋 · Ocean](#ocean--odb)
- [海象與氣象 · Marine & Weather](#marine--weather--cwa)
- [歷史觀測 · Historical Observations](#historical-observations)
- [水資源 · Water Resources](#water-resources)
- [能源與電網 · Energy & Grid](#energy--grid--台電)
- [空氣品質 · Air Quality](#air-quality--環境部)
- [地球物理與地震 · Geophysics & Seismic](#geophysics--seismic)
- [財經與產業 · Finance & Industry](#finance--industry--twsemops)
- [農業與漁業 · Agriculture & Fisheries](#agriculture--fisheries)
- [海洋保育 · Marine Conservation](#marine-conservation)
- [氣候變遷與災防 · Climate & Disaster](#climate--disaster)
- [衛星與再分析 · Satellite & Reanalysis](#satellite--reanalysis)
- [全球資料（台灣海域）· Global](#global-taiwan-region)
- [遙相關因子 · Climate Indices](#climate-indices)
- [分析工具 · Tools](#tools)
- [混淆稽核案例 · Worked Examples](#worked-examples)
- [Related lists](#related-lists)

## Ocean · ODB
[海洋學門資料庫 ODB](https://www.odb.ntu.edu.tw/)（台大海研所）提供多個免金鑰海洋 API，維護者 [@cywhale](https://github.com/cywhale)。

- **[海洋熱浪 MHW API](https://eco.odb.ntu.edu.tw/pub/MHW/)**：全球 0.25° 月尺度 SST、距平、MHW 分級（Hobday 2018）。
- **[ODB OPeNDAP](https://oceanapi.cwa.gov.tw/opendap/hyrax/)**：OISST、OCM、HYCOM、WW3 網格資料；鏡像有掉值污染，長序列改用 NOAA ERDDAP。
- **[CTD／SADCP API](https://ecodata.odb.ntu.edu.tw/api/ctd)**：溫鹽剖面與海流剖面，但是氣候平均、不是時間序列。
- **[Hidy Viewer](https://odbview.oc.ntu.edu.tw/hidy/)**、**[MHW 儀表板](https://eco.odb.ntu.edu.tw/pub/MHW/)**：互動地圖與海洋熱浪儀表板。
- 更多（GEBCO 水深、GLODAP、WOA23、Argo、潮汐、航次）見 [@cywhale 的 repos](https://github.com/cywhale?tab=repositories)；引用規範見 [資料政策](https://github.com/oceandatabank/Data-policy)。

## Marine & Weather · CWA
[交通部中央氣象署](https://www.cwa.gov.tw/)。

- **[CWA 開放資料 S3](https://cwaopendata.s3.ap-northeast-1.amazonaws.com/?list-type=2)**：完全公開、可列目錄的海氣象資料桶（含大潮、長浪、即時海況、日均海溫）。
- **[站級與預報海象 API](https://ocean.cwa.gov.tw/)**：免登入取逐時 SST、海流、波、風、水位與 7 天預報。
- **[歷史海象下載](https://ocean.cwa.gov.tw/V2/data_interface/datasets)**：潮位站與浮標的逐年歷史，需免費註冊。
- **[海象測站清單](https://oceanapi.cwa.gov.tw/restapi/v2/static/station/station_info.html)**：潮位站、資料浮標、波浪站，多數含海溫（站表含已停測站，站數會變動）。
- **[opendata 平台](https://opendata.cwa.gov.tw/)**：氣象、地震、海象預報產品，datastore API 需金鑰。

## Historical Observations
- **[Raingel/historical_weather](https://github.com/Raingel/historical_weather)**：CWA CODIS 陸上測站逐時，GitHub 上機器可讀 CSV、每日更新，可補救滾動視窗會掉的資料。
- **[CODiS](https://codis.cwa.gov.tw/)**：CWA 氣候觀測資料查詢（陸上站歷史，含澎湖浮標 SST 2007 起）。
- **[農業氣象觀測網](https://agr.cwa.gov.tw/)**：農業站，只有氣溫、土溫，無水溫。

## Water Resources
[經濟部水利署](https://www.wra.gov.tw/)。

- **[水庫即時水情](https://data.gov.tw/dataset/45501)**：有效蓄水量、蓄水率、水位，每小時。
- **河川流量**：目前沒有穩定的免金鑰端點（水情 API 會 503）；在拿到實測流量前，降水只能當氣象乾旱的粗略 covariate，不是流量代理。

## Energy & Grid · 台電
[台灣電力公司開放資料](https://www.taipower.com.tw/tc/page.aspx?mid=5554)，一組 `apply/file` 檔案，全部免金鑰。

- **即時各機組發電與裝置容量**（`d006001`）：216 個機組，含 22 座太陽能場的裝置容量，很珍貴。
- **十分鐘級各機組淨發電**（`d006010`）：約 200 萬筆／192 MB，目前只發布 2026 Q1 一檔。
- **[每日供需與風力、太陽能](https://data.gov.tw/dataset/19995)**（`d006005`）：尖峰時點快照，2025 起。
- **[每日備轉容量率](https://data.gov.tw/dataset/24945)**（`d006004`）：2023 起，序列較長。

## Air Quality · 環境部
[環境部環境資料開放平臺](https://data.moenv.gov.tw/)（前身環保署）。

- **[AQI 歷史（逐時、站級）](https://data.moenv.gov.tw/dataset/detail/aqx_p_488)**（`aqx_p_488`）：含 PM10、PM2.5、風速、**風向**、座標，可做「下風」空間分析，可回溯到 2016-11。
- **即時 AQI**（`aqx_p_432`）、**PM2.5**（`aqx_p_02`）。
- **金鑰**：正式做法是到平台免費註冊；急用時 data.gov.tw 的下載欄位公告了公用 key（可能輪替，不保證持續可用）。
- **多年份批量**：走 [airtw 歷史資料](https://airtw.moenv.gov.tw/CHT/Query/His_Data.aspx) 的分區逐年封存（2018–2025）。

## Geophysics & Seismic
- **[USGS FDSN 地震目錄](https://earthquake.usgs.gov/fdsnws/event/1/)**：免金鑰、歷史完整、可框台灣範圍。長歷史用它，不用需金鑰的 CWA 目錄。
- **[CWA 地震測報中心](https://scweb.cwa.gov.tw/)**：顯著有感地震報告（人工查詢介面）。

## Finance & Industry · TWSE/MOPS
把產業、經濟接進環境分析（例如地震 × 晶圓廠、氣候 × 農產）時的公開來源。

- **[TWSE 每日個股股價](https://www.twse.com.tw/)**：免金鑰，一次一個月。
- **[MOPS 上市公司月營收](https://mopsov.twse.com.tw/)**：逐月靜態頁（要用 `mopsov`，不是 `mops`）。

## Agriculture & Fisheries
[農業部](https://www.moa.gov.tw/) / [漁業署](https://www.fa.gov.tw/)。

- **[農業部開放資料](https://data.moa.gov.tw/)**：走 `DataFileService.aspx`，不是 `/api/...`。
- **[漁業統計年報](https://www.fa.gov.tw/view.php?theme=FS_AR)**：產量、貿易、設施（無災害章節；事件級災損需登入）。

## Marine Conservation
- **[海洋保育網 iOcean](https://iocean.oca.gov.tw/)**（海保署）：珊瑚健康公民回報、海洋資料匯流。
- **[Reef Check Taiwan](https://coral.relab.cc/)**：珊瑚礁體檢，多為 PDF 與公民回報。

## Climate & Disaster
- **[TCCIP 氣候變遷推估](https://tccip.ncdr.nat.gov.tw/)**（NCDR）：AR6 降尺度推估、養殖漁業冷熱危害指標、災害衝擊。
- **[NOAA Niño3.4 / ONI](https://psl.noaa.gov/data/timeseries/month/data/nino34.long.anom.data)**：ENSO 指數，1870 起。

## Satellite & Reanalysis
- **[NASA POWER](https://power.larc.nasa.gov/)**：逐日、逐點、免金鑰的太陽輻照、氣膠、氣象一站取得，做能源 × 空品分析的主力。
- **[TASA 國家太空中心](https://www.tasa.org.tw/)**：FORMOSAT 系列、Triton（GNSS 反射估海面風），以任務為主。

## Global (Taiwan-region)
台灣海域可用的全球開放資料。

- **[NOAA OISST v2.1 via ERDDAP](https://coastwatch.pfeg.noaa.gov/erddap/griddap/ncdcOisst21Agg_LonPM180.html)**：逐日 0.25°、1981 起，打台灣事件的主力。
- **[NOAA Coral Reef Watch](https://coralreefwatch.noaa.gov/)**：全球 5 km DHW、白化警報（台灣有兩個虛擬站）。
- **[Copernicus Marine](https://data.marine.copernicus.eu/)**：GLORYS12V1 全球再分析，1993 起。
- **[NASA Earthdata](https://www.earthdata.nasa.gov/topics/ocean)**、**[Ocean Color](https://oceandata.sci.gsfc.nasa.gov/)**。

## Climate Indices
把台灣的海溫、極端事件放進驅動因子的脈絡。這些因子要實測、別假設（本清單實測 DJF ENSO 對台灣近海冬季海溫距平的零延遲相關只有 −0.03）。

- **[ENSO](https://psl.noaa.gov/enso/mei/)**（Niño3.4、ONI、MEI）、**[PDO](https://www.ncei.noaa.gov/access/monitoring/pdo/)**、**[IOD/DMI](https://psl.noaa.gov/gcos_wgsp/Timeseries/DMI/)**、**[MJO](https://www.cpc.ncep.noaa.gov/products/precip/CWlink/MJO/mjo.shtml)**、**[NAO/AO](https://www.cpc.ncep.noaa.gov/products/precip/CWlink/daily_ao_index/ao.shtml)**。
- **[黑潮 Kuroshio](https://data.marine.copernicus.eu/)**（衛星測高導出）、**[ERA5](https://cds.climate.copernicus.eu/)**（大氣再分析）。

## Tools
- **[ecjoliver/marineHeatWaves](https://github.com/ecjoliver/marineHeatWaves)**（Python）、**[heatwaveR](https://robwschlegel.github.io/heatwaveR/)**（R）：Hobday MHW／MCS 參考實作。
- **[NODASS 2026 教材](https://github.com/ODBapp/2026_NODASS_workshop)**：ODB 海洋資料分析範例。

## Worked Examples
用上面這些資料做的四個跨領域題目，四個誠實的案例、四種不同的推論失敗。每個 repo 都把會騙人的那一步留在程式碼裡可重跑，並附共用的 [混淆稽核清單](https://github.com/thc1006/taiwan-earthquake-fab/blob/main/CONFOUND-AUDIT.md)。

| 題目 | 天真結果 | 推論失敗 |
|---|---|---|
| [taiwan-solar-dimming](https://github.com/thc1006/taiwan-solar-dimming) | 氣膠 β = −85，p < 0.0001 | 季節同步 |
| [taiwan-earthquake-fab](https://github.com/thc1006/taiwan-earthquake-fab) | 點估計為負且合理 | 檢定力不足、測不到 |
| [taiwan-riverbed-dust](https://github.com/thc1006/taiwan-riverbed-dust) | 下風 +0.65，p = 0.0001 | 順風向空間梯度 |
| [taiwan-vre-drought](https://github.com/thc1006/taiwan-vre-drought) | 相關 r = +0.51 | 會計恆等式 |

## Related lists
台灣（範圍不同或較一般，與本清單互補）：
- [onlinemad/awesome-opendata-taiwan-gov](https://github.com/onlinemad/awesome-opendata-taiwan-gov) — 台灣政府開放資料與 API，泛用取向
- [lun916/awesome-taiwan-business-data](https://github.com/lun916/awesome-taiwan-business-data) — 公司、商業登記、政府標案
- [mosdeo/List_of_Taiwan_OpenData_Diamonds_and_Rust](https://github.com/mosdeo/List_of_Taiwan_OpenData_Diamonds_and_Rust) — 政府開放資料踩雷筆記
- [cm-ailab-cc-plugins/plugin-twinkle-hub](https://github.com/cm-ailab-cc-plugins/plugin-twinkle-hub) — 台灣開放資料 MCP 聚合端點

全球：
- [pangeo-data/awesome-open-climate-science](https://github.com/pangeo-data/awesome-open-climate-science)、[Inria-Chile/awesome-ocean-ai-data](https://github.com/Inria-Chile/awesome-ocean-ai-data)、[monocilindro/Awesome-Hydrospatial](https://github.com/monocilindro/Awesome-Hydrospatial)

## Contributing
歡迎 PR，做法見 [CONTRIBUTING.md](CONTRIBUTING.md)。原則：每個來源要實際測過，附存取方法與（若有）踩坑，並遵循 [awesome 準則](https://github.com/sindresorhus/awesome/blob/main/awesome.md)。參與請一併遵守[行為準則](CODE_OF_CONDUCT.md)。

## License
清單內容以 [CC0](LICENSE) 釋出。各資料源的著作權與授權，依其原始規定。
