# Data Sources

## 1. Fahrraddiebstahl in Berlin

**Source**
Berlin Open Data / Polizei Berlin  
https://daten.berlin.de/datensaetze/fahrraddiebstahl-in-berlin

**Direct CSV**
https://www.polizei-berlin.eu/Fahrraddiebstahl/Fahrraddiebstahl.csv

**Purpose in project**
Primary label dataset for theft incidents. Used to build area-time theft risk targets.

**Granularity**
Planungsraum-level incident data.

**Refresh cadence**
Updated daily.

**Important fields to inspect**
- Tatzeit / date fields
- Planungsraum or area code
- bike type
- damage / value fields
- attempt / completion fields

**Planned usage**
Aggregate to `planungsraum x day` for the baseline model.

**Known limitations**
Contains reported theft incidents, not all safe parking events. Should be used for relative risk estimation, not exact theft prediction for one specific bike.


## 2. LOR / Lebensweltlich orientierte Räume (01.01.2021)

**Source**
Amt für Statistik Berlin-Brandenburg  
https://daten.berlin.de/datensaetze/lebensweltlich-orientierte-raume-lor-01-01-2021-wfs-34c86848

**Purpose in project**
Official geometry layer for mapping and spatial joins.

**Granularity**
Three spatial levels:
- Prognoseraum (58)
- Bezirksregion (143)
- Planungsraum (542)

**Layer needed for MVP**
Planungsraum (PLR)

**Format / access**
WFS service, viewable and downloadable via WFS Explorer.

**Planned usage**
- Render Berlin heatmap
- Join theft aggregates to official polygons
- Support later recommendation and neighborhood analysis

**Local storage**
- raw: `data/raw/lor/`
- cleaned: `data/interim/lor/`
- processed map output: `data/processed/`

**Important fields to inspect**
- Planungsraum code
- Planungsraum name
- parent district / region fields
- geometry
- CRS

**Known limitations**
Static boundary dataset dated 01.01.2021. Must verify that the area code format matches the theft dataset exactly.


## 3. Radzähldaten in Berlin

**Source**
Berlin Open Data  
https://daten.berlin.de/datensaetze?groups=verkehr

**Purpose in project**
Exposure proxy for bicycle activity. Useful for improving risk estimates.

**Planned usage**
Join nearby or area-level cycling activity to theft-risk features.

**Notes**
Likely useful after the first baseline is working.


## Initial findings to verify during exploration

- Which column in the theft dataset contains the Planungsraum identifier?
- Which column in the LOR data is the matching Planungsraum code?
- Do both datasets use the same identifier format?
- Which date columns in the theft data are most reliable for daily aggregation?
- Which CRS is used by the LOR geometry?
- Can the WFS layer be exported cleanly to GeoJSON or GeoPackage for local use?