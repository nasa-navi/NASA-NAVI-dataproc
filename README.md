# 🛰️ NASA NAVI Data Pipeline (`nasa-navi-data`)

**Submodule of the AIRBENDER Project: NASA TEMPO-based Personalized Air Quality Forecasting System**

---

**Members:**  
- [JIHO RYU](https://github.com/ryujihos0105)  
- [JEONG AH YOON](https://github.com/jjyoon012-git)  
- [Jihoon Jung ](https://github.com/jeehun3020)

**[DEMO: AIRBENDER Demo Video (YouTube)](https://www.youtube.com/watch?v=VvfAQruD_C0)**

---

## 1. Overview

This repository contains **data acquisition and preprocessing scripts** for NASA’s **TEMPO satellite atmospheric products**, supporting the main **AIRBENDER** forecasting pipeline.  
All scripts within the `tempo/` directory automate the process of downloading, converting, and synchronizing **TEMPO Level-3 data** for **NO₂, O₃, and HCHO**.

The generated CSV outputs are used for:
- Air quality prediction model training (Vertex AI AutoML)
- Correlation and trend analysis between atmospheric pollutants
- Temporal alignment with ground-based OpenAQ and meteorological data

---

## 2. Repository Structure

```
nasa-navi-data/
│
├── tempo/
│   ├── tempo_no2_l3_download.py        # Download NO₂ L3 NetCDF from NASA Earthdata
│   ├── tempo_no2_l3_to_csv.py          # Convert NO₂ NetCDF → CSV
│   ├── tempo_no2_l3_nyc_time.py        # Extract & resample NO₂ hourly data (NYC bbox)
│   │
│   ├── tempo_o3_l3_download.py         # Download O₃ L3 NetCDF
│   ├── tempo_o3_l3_to_csv.py           # Convert O₃ NetCDF → CSV
│   ├── tempo_o3_l3_nyc_time.py         # Extract & merge O₃ time column
│   │
│   ├── tempo_hcho_l3_to_csv.py         # Convert HCHO NetCDF → CSV
│   ├── tempo_hcho_l3_nyc_time.py       # Merge time column for HCHO dataset
│
└── README.md
```

Each script is modular and independently executable, but designed to form a unified preprocessing pipeline.

---

## 3. Data Sources

### 3.1 NASA TEMPO (Tropospheric Emissions: Monitoring of Pollution)
- **Instrument:** Geostationary UV-Visible spectrometer
- **Coverage:** North America (hourly observations)
- **Resolution:** ~8 km × 5 km
- **Products Used:**
  - NO₂ L3 V03 → `C2930763263-LARC_CLOUD`
  - HCHO L3 V03 → `C2930761273-LARC_CLOUD`
  - O₃ L3 V03 → `C2930784064-LARC_CLOUD`

### 3.2 Supplementary Sources
- **OpenAQ API** — Ground-based pollutant measurements (PM₂.₅, O₃)
- **Open-Meteo API** — Weather data (temperature, humidity, wind speed)

All datasets are merged on UTC timestamps and spatially filtered to the **New York City region (BBOX: -74.3, 40.4, -73.6, 41.0)**.

---

## 4. Data Processing Workflow

| Step | Script | Description |
|------|---------|-------------|
| 1 | `tempo_*_download.py` | Download raw NetCDF data from NASA Earthdata Cloud via `earthaccess` |
| 2 | `tempo_*_to_csv.py` | Convert NetCDF to structured CSV using `xarray` and `pandas` |
| 3 | `tempo_*_nyc_time.py` | Filter data for NYC bounding box and merge hourly timestamps |
| 4 | (Optional) Merge Outputs | Combine pollutant datasets for unified model input |

All CSV files follow a standardized schema:
```
timestamp_utc, lat, lon, value, variable
```

---

## 5. Example Usage

```bash
# (1) Activate environment
conda activate airbender

# (2) Run download + conversion for NO₂
python tempo/tempo_no2_l3_download.py
python tempo/tempo_no2_l3_to_csv.py

# (3) Align time dimension and filter for NYC
python tempo/tempo_no2_l3_nyc_time.py
```

---

## 6. Output Files

| Variable | File Example | Description |
|-----------|--------------|--------------|
| NO₂ | `tempo_no2_l3_nyc.csv` | Hourly NO₂ columns for NYC (ppbv) |
| O₃ | `tempo_o3_l3_nyc.csv` | Hourly ozone columns (ppbv) |
| HCHO | `tempo_hcho_l3_nyc.csv` | Hourly formaldehyde concentration |
| Combined | `tempo_merged.csv` | Merged dataset for Vertex AI input |

---

## 7. Dependencies

| Library | Version | Purpose |
|----------|----------|----------|
| `xarray` | ≥2024.1 | NetCDF parsing |
| `pandas` | ≥2.0 | Data manipulation |
| `earthaccess` | ≥0.10 | NASA Earthdata API access |
| `numpy` | ≥1.26 | Numerical operations |

---

## 8. Integration with AIRBENDER Main System

The processed CSV outputs are automatically uploaded to **Google Cloud Storage (GCS)** for:
- Input to **Vertex AI Forecasting**
- Visualization in **Streamlit / Flask dashboards**
- LLM-driven air-quality insight generation

---

## 🌍 From Satellite to Action
> *Turning NASA TEMPO atmospheric data into real-world environmental intelligence.*
