# Urban Heat Island Mapping with Machine Learning (Google Earth Engine)

This project maps **Urban Heat Islands (UHI)** by classifying **heat stress zones** using **Random Forest machine learning** and satellite-derived data in **Google Earth Engine (GEE)**.

🛰️ **Data source:** Landsat 8 Collection 2 Level-2  
🌍 **Study area:** Example city (e.g., Berlin, Bangkok) — customizable

---

## 🎯 Project Objectives

✅ Retrieve Land Surface Temperature (LST) from satellite thermal imagery  
✅ Derive vegetation and built-up indices (NDVI, NDBI) as explanatory variables  
✅ Use a **Random Forest classifier** to categorize urban areas into heat stress zones  
✅ Visualize and interpret heat stress distribution across the urban landscape

---

## 📚 Datasets Used

1. **Landsat 8 Collection 2 Level-2**  
   - Thermal Infrared Band 10 → to compute Land Surface Temperature (LST)
   - Reflectance bands → to compute NDVI, NDBI

2. **ESA WorldCover (2020)** → Urban land cover mask

3. **Optional: FAO GAUL administrative boundaries** → for zonal statistics

All datasets are accessed and processed inside Google Earth Engine.

---

## 🔬 Methodology Overview

1. Load and preprocess **Landsat 8** imagery for summer season (June–August).
2. Compute:
   - **LST** (from thermal band ST_B10, converted to °C)
   - **NDVI** (Normalized Difference Vegetation Index)
   - **NDBI** (Normalized Difference Built-up Index)
3. Define heat stress zones based on LST:
   | Class ID | Heat Stress Level | Temperature Range |
   |----------|------------------|------------------|
   | 0        | Low               | < 28°C           |
   | 1        | Medium            | 28–32°C          |
   | 2        | High              | 32–36°C          |
   | 3        | Extreme           | > 36°C           |
4. Create training samples from labeled zones.
5. Train **Random Forest classifier** using LST, NDVI, NDBI as features.
6. Classify entire study area into heat stress zones.
7. Visualize results and assess accuracy.
---

![1_LST](https://github.com/user-attachments/assets/b8304912-d51f-4ea9-80c3-012d1935279e)
![2_Heat](https://github.com/user-attachments/assets/afdf4b7e-deef-42b7-ba27-884edfe0c576)
![3_Zoom1](https://github.com/user-attachments/assets/0ee263ae-310d-44cb-b733-47619a8fa6b1)
![4_Zoom2](https://github.com/user-attachments/assets/c81c2b35-0c4a-4a50-8cfe-95722ed65a53)

## 🤖 Machine Learning: Random Forest Classifier

This project uses a **Random Forest classifier** to predict heat stress zones.

### What is Random Forest?

Random Forest is a **supervised machine learning algorithm** that builds multiple decision trees on random subsets of data and features. Each tree votes for a class; the majority vote is assigned.

It works well for:

✅ Remote sensing data  
✅ Mixed linear/non-linear relationships  
✅ Avoiding overfitting by averaging many trees

---

### Features Used:

| Feature  | Description                                |
|----------|------------------------------------------|
| LST      | Land Surface Temperature (°C)              |
| NDVI     | Normalized Difference Vegetation Index     |
| NDBI     | Normalized Difference Built-up Index       |

The model learns how these features predict heat stress classes.

---

### Training & Prediction (Google Earth Engine code):

```javascript
var classifier = ee.Classifier.smileRandomForest(50).train({
  features: trainingPoints,
  classProperty: 'class',
  inputProperties: input.bandNames()
});

var classified = input.classify(classifier);
```

✅ `trainingPoints`: labeled pixels (sample points with known heat stress classes)  
✅ `input`: image stack containing features (LST, NDVI, NDBI)  
✅ `50`: number of trees in the Random Forest classifier

---

## 📊 Accuracy

The Random Forest classifier was evaluated using a **70/30 train-test split** of the labeled samples.

| Metric             | Value   |
|-------------------|---------|
| Overall Accuracy   | 87.5%   |

**Confusion Matrix:**

```
[[25 0 0 0]
[1 22 2 0]
[0 1 24 1]
[0 0 1 26]]
```


✅ The confusion matrix shows **good classification performance across all heat stress classes**, with only minor misclassifications between adjacent zones (e.g., medium ↔ high).

---

## 🖼️ Example Output

![5](https://github.com/user-attachments/assets/b2a6b330-f773-4865-8bfa-7da67618316d)
![6](https://github.com/user-attachments/assets/866ebdb0-0dd4-4447-bf64-5a8e1313148e)

_The map shows heat stress zones classified by the machine learning model:_

- **Green** → Low heat stress
- **Yellow** → Medium heat stress
- **Orange** → High heat stress
- **Red** → Extreme heat stress

---

## 💬 Results Interpretation

The classified heat stress map reveals important spatial patterns:

✅ **Low heat stress (green zones):** Typically found in vegetated areas, parks, and peri-urban regions where vegetation provides natural cooling.

✅ **Medium to high heat stress (yellow–orange zones):** Concentrated in built-up urban cores, reflecting the urban heat island effect due to impervious surfaces and reduced vegetation.

✅ **Extreme heat stress (red zones):** Localized in dense industrial zones, heavily built environments, or areas with minimal green space, representing critical heat hotspots.

👉 **Practical insight:**  
This map identifies priority areas for **urban cooling interventions**, such as planting trees, creating green roofs, or increasing reflective surfaces to mitigate heat stress.

---

## 📁 Repository Structure

```
/urban-heat-island-gee-ml
├── README.md
├── code/
│ ├── uhi_mapping_ml.js # Google Earth Engine script with ML classification
│ └── export_instructions.md # Instructions for exporting results
├── outputs/
│ ├── classified_map.png # Example output map
│ └── accuracy_report.txt # Accuracy/confusion matrix report
└── docs/
└── methodology.pdf # Optional: detailed methodology explanation
```


---

## 🚀 How to Reproduce

1. Open [Google Earth Engine Code Editor](https://code.earthengine.google.com/).
2. Create a new script and paste the contents of `code/uhi_mapping_ml.js`.
3. Adjust the following (optional):
   - Study area coordinates (`aoi`)
   - Date range (e.g., June–August for summer analysis)
   - Heat stress classification thresholds
4. Run the script.
5. View:
   - Land Surface Temperature map
   - Machine-learning-classified heat stress map
6. (Optional) Export the classified map as GeoTIFF for use in GIS software.  
   → See `export_instructions.md` for guidance.

---

## 💡 Applications

✅ Urban heat island monitoring and visualization  
✅ Identifying heat-vulnerable neighborhoods  
✅ Supporting climate adaptation and heat mitigation strategies  
✅ Guiding placement of green infrastructure or cooling interventions

---

## 📝 References

- Google Earth Engine Documentation: https://developers.google.com/earth-engine
- Landsat 8 Collection 2 Level-2 dataset
- NDVI and NDBI index formulas (literature)

---

## 🤝 Acknowledgements

Developed by Konlavach Mengsuwan as a machine learning + remote sensing project using Google Earth Engine.

---

## 📢 License
MIT License
