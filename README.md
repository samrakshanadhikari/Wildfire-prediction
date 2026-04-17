# 🔥 Short-Term Wildfire Burned Area Prediction in California Using Multi-Source Environmental Features and Deep Learning

This project presents a **deep learning pipeline for wildfire burned area prediction** in California using multi-source remote sensing and environmental data. The model predicts pixel-level burn probability maps from environmental conditions and is evaluated on temporally held-out fire seasons.

> **Presented at:** TXST STEM Conference — Poster #36F-U  
> **Authors:** Samrakshyan Adhikari¹, Eunsang Cho²  
> ¹Department of Computer Science, ²Ingram College of Engineering, Texas State University, Texas, USA

---

## 📌 Objective

To develop and evaluate a machine learning framework that:

- Learns spatial patterns of wildfire risk from historical burned area events
- Generalizes to unseen future fire seasons (2020–2024) after training on past data (2001–2017)
- Handles severe class imbalance (fire pixels are a small minority)
- Produces interpretable, pixel-level probabilistic burn probability maps

---

## ❓ Research Questions

1. How accurately can burned area extent be predicted using lagged environmental and vegetation data?
2. How well does the model generalize to unseen regions and future wildfire seasons (2020–2024)?
3. Which environmental features (e.g., NDVI, EVI, slope, climate variables) are most important for burned area prediction?

---

## 🗺️ Study Area

California, United States — a region characterized by a Mediterranean climate with dry summers and hot, dry conditions that increase wildfire risk.

---

## 🛰️ Data Sources

### **Burned Area (Labels)**
- **MODIS Burned Area Product (MCD64A1)** — binary burned vs. non-burned targets

### **Environmental Predictors**

| Type | Variable | Full Name | Value | Resolution | Source |
|------|----------|-----------|-------|------------|--------|
| Vegetation | NDVI | Normalized Difference Vegetation Index | Mean | 250→500m | MODIS MOD13Q1 |
| Vegetation | EVI | Enhanced Vegetation Index | Mean | 250→500m | MODIS MOD13Q1 |
| Vegetation | NDVI_change | NDVI Temporal Change | Difference (t-1 − t-2) | 250→500m | Derived |
| Vegetation | EVI_change | EVI Temporal Change | Difference (t-1 − t-2) | 250→500m | Derived |
| Climatic | Temp_t1 | Temperature (Lag 1 Month) | Monthly Mean (°C) | ~4,000m | GRIDMET |
| Climatic | Temp_t2 | Temperature (Lag 2 Months) | Monthly Mean (°C) | ~4,000m | GRIDMET |
| Climatic | Prec_t1 | Precipitation (Lag 1 Month) | Monthly Sum | ~4,000m | GRIDMET |
| Climatic | Prec_t2 | Precipitation (Lag 2 Months) | Monthly Sum | ~4,000m | GRIDMET |
| Climatic | Wind_t1 | Wind Speed (Lag 1 Month) | Monthly Mean | ~4,000m | GRIDMET |
| Climatic | Wind_t2 | Wind Speed (Lag 2 Months) | Monthly Mean | ~4,000m | GRIDMET |
| Climatic | VPD_t1 | Vapor Pressure Deficit (Lag 1 Month) | Monthly Mean | ~4,000m | GRIDMET |
| Climatic | VPD_t2 | Vapor Pressure Deficit (Lag 2 Months) | Monthly Mean | ~4,000m | GRIDMET |
| Physical | Elevation | Elevation | Static | 30m | SRTM DEM |
| Physical | Slope | Terrain Slope | Derived | 30m | SRTM DEM |
| Physical | Aspect | Terrain Aspect | Derived | 30m | SRTM DEM |
| Land Cover | Landcover | Land Cover Classification | Annual Class | 500m | MODIS MCD12Q1 |

---

## 🌎 Processing Pipeline (Google Earth Engine)

1. All rasters reprojected to **EPSG:5070** at 500m resolution
2. Environmental data aggregated to monthly means/sums
3. Lagged features computed over 2-month windows
4. Raster stacks built per time period
5. Exported to **Google Cloud Storage**
6. **32×32 spatial patches** extracted for training

---

## 🧠 Model

An **Attention U-Net** segmentation network is trained for per-pixel burned area prediction.

### Architecture

- **Encoder:** Convolutional blocks with progressive feature extraction
- **Bottleneck:** Deepest feature representation
- **Decoder:** Transposed convolutions with skip connections
- **Attention Gates:** Suppress irrelevant spatial activations and focus on fire-prone regions
- **Output:** 1-channel burn probability map (sigmoid activation)

---

## 🎯 Training Strategy

### Temporal Train/Test Split

| Split | Period | Patches | Fire Pixel % |
|-------|--------|---------|--------------|
| Train | 2001–2017 | 17,868 | 3.10% |
| Test | 2020–2024 | 47,328 | 6.89% |

- No temporal leakage between splits
- Simulates **real deployment**: training on historical fires, predicting future seasons

### Loss & Optimization

- **Loss:** `BCEWithLogitsLoss` with class-balanced weighting
- **Optimizer:** Adam (lr=1e-3)
- **Batch size:** 32
- **Early stopping:** patience=12 (based on validation IoU)
- **Epochs:** up to 80

### Data Augmentation

- Random horizontal/vertical flips and 90° rotations
- Brightness/contrast jitter
- Gaussian noise injection
- Random band dropout

---

## 📊 Results

### Model Performance (Test Set: 2020–2024)

| Metric | Value |
|--------|-------|
| F1-Score | 0.1800 |
| Precision | 0.2169 |
| Recall | 0.1888 |
| AUC-PR | 0.1628 |

The Attention U-Net demonstrates basic capability in learning wildfire patterns but moderate performance reflects the challenge of cross-temporal generalization and severe class imbalance.

### Feature Variable Importance

- **Vegetation indices (NDVI, EVI)** are the most important predictors
- **Climate variables** (temperature, precipitation, VPD) contribute moderately
- **Terrain features** (elevation, slope) have lower relative influence

### Key Observations

- The model captures major burned regions but misses smaller fire areas (false negatives)
- Some false alarms (false positives) occur in vegetation-dense regions
- High class imbalance leads to a precision-recall tradeoff

---

## ✅ Strengths of This Approach

- Temporal generalization testing (train on past, test on future seasons)
- Attention mechanism improves spatial focus on fire-prone regions
- Interpretable probability outputs and confusion maps
- Scalable cloud-based preprocessing via Google Earth Engine
- Feature importance analysis for domain interpretability

---

## 🔭 Future Work

- **Advanced Loss Functions:** Focal Loss and Tversky Loss to improve recall on minority fire pixels
- **Temporal Sequence Modeling:** Replace 2-month lagged static features with **7-day temporal sequences** using **Conv-LSTM** to capture short-term fire weather dynamics
- **Transformer-Based Models:** Explore Temporal U-Net and Transformer architectures for long-range spatial dependencies
- **Additional Data Sources:** Wildfire datasets post-2018, drought indices, soil moisture
- **Post-Processing:** Morphological region-growing for better fire boundary delineation

---

## 📚 References

- Zhang, Q., Ge, L., Zhang, R., Metternicht, G. I., Liu, C., & Du, Z. (2021). Deep-learning-based burned area mapping using the synergy of Sentinel-1 & Sentinel-2 data. *Remote Sensing of Environment*, 264, 112575.
- MODIS Burned Area Product (MCD64A1)
- MODIS Vegetation Indices
- GRIDMET Climate Data
- TerraClimate Drought Index
- NLCD Land Cover
- Google Earth Engine

---

## 👩‍💻 Author

**Samrakshyan Adhikari**  
B.S. Computer Science (Computer Engineering), Honors Program  
Texas State University  
Portfolio: https://samrakshanadhikari.github.io/WebsitePortfolio/  
Email: samrakshanadhikari@gmail.com

---

## 📌 License

This repository is for academic & research demonstration purposes only.
