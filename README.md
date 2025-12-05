 # 🔥 Wildfire Ignition Risk Prediction in California (2000–2025)

This project presents a **deep learning pipeline for wildfire ignition risk modeling** in California using multi-source remote sensing data and event-based evaluation. The model predicts pixel-level burn probability maps from environmental conditions prior to ignition.

The goal is not only to predict burned pixels, but to **evaluate cross-event generalization**:  
> Can a model trained on past wildfires predict a completely unseen fire in a different year and region?

---

## 📌 Objective

To develop and evaluate a machine learning framework that:

- Learns spatial patterns of wildfire risk from historical events
- Predicts generalizable risk maps for unseen wildfires
- Handles severe class imbalance
- Produces interpretable, pixel-level probabilistic outputs

---

## 🛰️ Data Sources

### **Burned Area (Labels)**
- **MTBS / MODIS Burned Area**
- Seasonal fire footprints aggregated into binary burned vs non-burned targets

### **Environmental Predictors (14 bands)**

| Category | Dataset | Variables |
|----------|--------|-----------|
| Climate  | GRIDMET | tmax, precipitation, wind speed, humidity |
| Drought  | TerraClimate | PDSI |
| Vegetation | MODIS | NDVI |
| Fuels | LFMC Mapper | Live fuel moisture |
| Topography | SRTM | elevation, slope, aspect_sin, aspect_cos |
| Land cover | NLCD | landcover class |
| Temporal lags | Derived | 3-month accumulated precipitation, 3-month mean VPD |

---

## 🌎 Processing Pipeline (Google Earth Engine)

Environmental variables are processed and exported from Google Earth Engine:

1. All rasters reprojected to **EPSG:5070** at 500m resolution.
2. Environmental data aggregated to monthly means/sums.
3. Lagged features computed (previous-month, 3-month windows).
4. EVM (Environmental Variable Map) stacks built per fire event.
5. Rasters exported to **Google Cloud Storage**.
6. 32×32 spatial patches extracted for training.

---

## 🧠 Model

A **small U-Net** segmentation network is trained for per-pixel burned-area prediction.

### Architecture:

- Encoder:
  - (C → 32) → (32 → 64) → max pooling
- Bottleneck:
  - (64 → 128)
- Decoder:
  - Transposed convolutions with skip connections
- Output:
  - 1-channel burn probability map

---

## 🎯 Training Strategy

### **Cross-Event Evaluation (Leave-One-Event-Out)**

For each wildfire event:

- **Training:** all other events
- **Testing:** the held-out fire
- No spatial leakage
- No temporal overlap

This simulates **real deployment**: predicting future wildfires from past ones.

---

### Loss & Optimization

- **Loss:** `BCEWithLogitsLoss` with class-balanced weighting
- **Optimizer:** Adam (lr=1e-3)
- **Batch size:** 32
- **Early stopping:** patience=12 (based on test IoU)
- **Epochs:** up to 80 per fold

---

### Data Augmentation

To improve generalization:

- Random flips and 90° rotations
- Brightness/contrast jitter
- Gaussian noise
- Random band dropout

---

## 🔍 Example Result — Atlas Fire (October 2017)

<p align="center">
  <img width="1543" height="398" alt="Atlassss" src="https://github.com/user-attachments/assets/d15c63b2-f25e-4475-b27f-b29d26eae52b" />


  <img width="1783" height="424" alt="cedar" src="https://github.com/user-attachments/assets/cf55dfcd-d2fb-4019-aebc-ffe227719783" />
ttachments/assets/599fefa9-8f76-43be-ba93-376951078cd2" />

<img width="1641" height="398" alt="thomas" src="https://github.com/user-attachments/assets/6bc24fa0-0fd6-4b24-a6be-65314cb6416c" />

</p>

**Panels:**
1. Raw MODIS burned area  
2. Cleaned binary target  
3. Burn probability predicted from prior-month conditions  
4. Confusion map (TP=green, FP=red, FN=blue)

**Metric (held-out event):**
- IoU = 0.219  
- F1-score = 0.359  

This illustrates the difficulty of predicting ignition patterns based solely on environmental conditions.


---

## ✅ Strengths of This Approach

- Event-based evaluation (not random splits)
- Realistic generalization testing
- Interpretable probability outputs
- Scalable via cloud-based preprocessing
- Emphasis on failure cases (confusion maps, IoU, F1)
- Clean separation of training vs inference pipelines

---

## 📚 References

- MTBS Burned Area Dataset  
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
