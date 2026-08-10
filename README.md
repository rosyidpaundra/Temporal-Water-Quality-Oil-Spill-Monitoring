# Temporal Water Quality & Oil Spill Monitoring (Google Earth Engine)

A Google Earth Engine (GEE) web app for **semester-based (6-month) temporal monitoring** of water quality, eutrophication risk, oil spills, and vessel presence in coastal waters, covering **January 2023 – June 2026 (3.5 years)**.

The app combines optical (Sentinel-2), thermal (Landsat 8), and radar (Sentinel-1 SAR) satellite imagery into a single interactive dashboard with a map, a period selector, and time-series charts.

## Features

- **Semester dropdown selector** – switch between 7 six-month periods (H1 2023 → H1 2026) to update the spatial map.
- **Eutrophication Risk Index (IRE)** – a weighted composite index built from:
  - NDCI (Normalized Difference Chlorophyll Index) – 50%
  - TSS (Total Suspended Solids, proxy) – 30%
  - SST (Sea Surface Temperature) – 20%
- **Oil spill detection** – identifies low-backscatter areas in Sentinel-1 SAR (VV < -18 dB) as candidate oil slicks.
- **Vessel (ship) detection** – identifies high-backscatter bright spots in SAR (VV > 0 dB) typical of metal hulls.
- **Chlorophyll-a estimation** – derived from NDCI using a linear regression formula.
- **8 fixed sampling points** – automatic extraction of Oil Status, SAR value, NDCI, Chl-a, and Ship Status at each point, per selected period.
- **CSV export to Google Drive** – results from the 8 sampling points are exported automatically for each period viewed.
- **Click-to-chart** – click anywhere on the water surface to generate 3.5-year historical trend charts (NDCI and SAR backscatter) for that exact location.

## Data Sources

| Dataset | Purpose | Resolution |
|---|---|---|
| `COPERNICUS/S2_SR_HARMONIZED` (Sentinel-2 SR) | NDCI, TSS, Chl-a | 10–20 m |
| `LANDSAT/LC08/C02/T1_L2` (Landsat 8) | Sea Surface Temperature (SST) | 30 m |
| `COPERNICUS/S1_GRD` (Sentinel-1 SAR, VV, IW mode) | Oil spill & ship detection | 10 m |
| `JRC/GSW1_4/GlobalSurfaceWater` | Water mask (occurrence > 80%) | 30 m |

## Study Area

A rectangular Area of Interest (AOI) defined by the polygon:

```
[106.60, -5.85], [107.05, -5.85], [107.05, -6.12], [106.60, -6.12]
```

(West Java coastal waters, Indonesia)

## Map Layers

| Layer | Description | Visualization |
|---|---|---|
| Radar Sentinel-1 | Raw SAR VV backscatter (dB) | Grayscale/red, -25 to 0 dB |
| Eutrophication Risk (IRE) | Composite eutrophication index | Blue → red gradient |
| Oil Spill | Masked low-backscatter areas | Purple |
| Ship Detection | Masked high-backscatter bright spots | Cyan |
| 8 Sample Points | Fixed monitoring coordinates | Yellow markers |
| Analysis Point | Last clicked location | Red marker |

## How It Works

1. On load, the app initializes with the most recent period (H1 2026) via `updateMap()`.
2. Selecting a period from the dropdown filters all three collections (Sentinel-2, Landsat 8, Sentinel-1) to that date range and recomputes the median composite, IRE, oil spill mask, and ship mask.
3. The 8 predefined sampling points are used to extract per-point values (oil status, SAR dB, NDCI, Chl-a, ship status) via `reduceRegions`, then exported as a CSV to a `GEE_Ekstraksi_Data` folder in Google Drive.
4. Clicking on the map triggers two full-history (2023–2026) time-series charts at that location: NDCI trend and SAR backscatter trend.

## Usage

1. Open the script in the [Google Earth Engine Code Editor](https://code.earthengine.google.com/). https://code.earthengine.google.com/ee53535dfbe5cbe2d5716609f2adef1e
2. Run the script.
3. Use the **"Pilih Periode Peta"** dropdown (top-left panel) to switch between semesters.
4. Click any point on the water to view its 3.5-year historical trend charts.
5. Check the **Tasks** tab in the Code Editor to manually run the CSV export to Google Drive for the currently selected period.

## Requirements

- A Google Earth Engine account with access to the Code Editor.
- Access to Google Drive (for CSV exports).

## Notes & Limitations

- TSS, SST, and IRE thresholds are simplified proxy formulas and should be calibrated against in-situ (field) measurements before use in operational decision-making.
- Oil spill and ship detection rely purely on SAR backscatter thresholds and may produce false positives (e.g., calm-water look-alikes, wind shadows) — visual/manual verification is recommended.
- Cloud filtering for Sentinel-2 and Landsat 8 is set at a maximum of 20% cloud cover; periods with persistent cloud cover may have sparse or no valid composites.

## License

Add your preferred license here (e.g., MIT).
