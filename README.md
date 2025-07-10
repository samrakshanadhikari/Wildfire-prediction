# 🔥 Wildfire Ignition Risk Prediction in California (2000–2025)

This project presents a deep learning-based framework to **predict seasonal wildfire ignition risk in California** using remotely-sensed environmental variables and historical burn data. Leveraging Convolutional Neural Networks (CNNs), we classify wildfire risk into three levels: **Low**, **Medium**, and **High** at a **500-meter resolution**, using a temporal span of **2000–2025**.

---

## 📌 Objective

To develop a data-driven wildfire ignition risk model using environmental predictors and past fire occurrence data, helping advance research in climate-informed fire management and risk forecasting.

---

## 🛰️ Data Sources

### 1. **Fire Occurrence (Labels: BIMs)**
- **Dataset**: Monitoring Trends in Burn Severity (MTBS)
- **Processing**:
  - Fires aggregated into **Binary Ignition Maps (BIMs)** per season/year.
  - Burn presence defined using percentiles (e.g., >33rd percentile = fire).
  - Resampled from 30m to 500m using majority aggregation.

### 2. **Environmental Variables (Inputs: EVMs)**

| Dataset     | Variables                                 | Source |
|-------------|--------------------------------------------|--------|
| PRISM       | Max Temperature, Min Temperature, Precipitation | [PRISM Climate Group](https://prism.oregonstate.edu/) |
| GRIDMET     | Wind Speed, Relative Humidity, ETo, Aridity Index | [GRIDMET](https://www.climatologylab.org/gridmet.html) |
| MODIS       | NDVI, LAI                                  | [MODIS Vegetation Indices](https://modis.gsfc.nasa.gov/) |
| SRTM        | Elevation, Slope                           | [SRTM](https://earthdata.nasa.gov/) |

- All environmental maps are processed per season, normalized, and aligned at **500m resolution**.

---

## 🌎 Spatial & Temporal Scope

- **Region**: California (USA)
- **Spatial Resolution**: 500 meters
- **Seasons**: DJF (Winter), MAM (Spring), JJA (Summer), SON (Fall)
- **Years**: 2000–2025

---

## 🧱 Dataset Preparation

### 🔄 Preprocessing Workflow

1. **Clip to California boundary** using Earth Engine.
2. **Generate Seasonal EVMs** from all environmental sources.
3. **Generate Seasonal BIMs** using MTBS fire footprints.
4. **Upsample/downsample** to 500m.
5. **Patch Extraction**: 32×32 spatial patches across all seasons/years.
6. **Balanced Sampling** across fire risk classes (0 = Low, 1 = Medium, 2 = High).

### 🧾 Format

- Input: `.tif` files → Extracted patches → TFRecords
- Storage: `Google Cloud Storage` bucket: `california-fire-model`
- Structure:


---

## 📚 References

- [MTBS Fire Data](https://mtbs.gov/)
- [PRISM Climate Group](https://prism.oregonstate.edu/)
- [GRIDMET](https://www.climatologylab.org/gridmet.html)
- [MODIS Vegetation Indices](https://modis.gsfc.nasa.gov/)
- [Google Earth Engine](https://earthengine.google.com/)

---

## 👩‍💻 Author

- Samrakshyan Adhikari 
- Undergraduate Researcher, Texas State University
- Contact: nbb38@txstate.edu
  

---

## 📌 License

This project is for research and educational purposes only.
