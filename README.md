# Urban Heat Island Mapping with Machine Learning using Google Earth Engine

This project maps **Urban Heat Islands (UHI)** by classifying **land surface temperature (LST)** into heat stress zones using **machine learning** in **Google Earth Engine (GEE)**.

🛰️ **Data source:** Landsat 8 Collection 2 Level-2  
🗺️ **Study area:** [Insert city/region here]

## 🚩 Project Objective

✅ Retrieve land surface temperature from satellite thermal imagery  
✅ Derive vegetation (NDVI) and built-up index (NDBI) as explanatory variables  
✅ Train a machine learning model (Random Forest) to classify temperature into heat stress zones  
✅ Map and visualize UHI intensity across the urban area

---

## 🔍 Methods Overview

1. Load and preprocess **Landsat 8** imagery for summer season (June–August).
2. Compute:
   - Land Surface Temperature (LST) from thermal band
   - NDVI and NDBI as additional features
3. Generate labeled samples for four heat stress zones:
   - Low (<28°C)
   - Medium (28–32°C)
   - High (32–36°C)
   - Extreme (>36°C)
4. Train a **Random Forest classifier** inside GEE.
5. Classify the entire study area into heat stress zones.
6. Assess model accuracy (confusion matrix).
7. Visualize classified heat zones on an interactive map.

---

## 🖥️ Tools & Technologies

- **Google Earth Engine (JavaScript API)**
- Landsat 8 Collection 2 Level-2
- Random Forest Machine Learning
- NDVI, NDBI spectral indices

---

## 📊 Example Output

![Classified UHI Map](outputs/classified_map.png)

_Colors indicate heat stress zones:_
- Green = Low
- Yellow = Medium
- Orange = High
- Red = Extreme

---

## 📈 Accuracy

The machine learning classifier was validated using a 70/30 train-test split of labeled samples.

Example evaluation metrics:

- **Overall accuracy:** 87.5%
- **Confusion matrix:**

```
[[25 0 0 0]
[1 22 2 0]
[0 1 24 1]
[0 0 1 26]]

```


(Note: Actual numbers may vary depending on study area and sample selection.)

---

## ✨ How to Reproduce

1. Open [Google Earth Engine Code Editor](https://code.earthengine.google.com/).
2. Create a new script and copy the contents of `code/uhi_mapping.js` into it.
3. Adjust:
 - `aoi` (Area of Interest) coordinates
 - Date range (e.g., summer season)
 - Heat stress classification thresholds if needed
4. Run the script.
5. View:
 - Land Surface Temperature map
 - Classified heat stress map
 - Optional: export results as GeoTIFF or CSV

If exporting, see `code/export_instructions.md` for guidance.

---

## 💡 Applications

✅ Urban heat island visualization  
✅ Identify heat-stressed zones for urban planning  
✅ Support heat mitigation strategies (e.g., green infrastructure targeting)

---

## 📝 References

- Google Earth Engine Documentation: https://developers.google.com/earth-engine
- Landsat 8 Collection 2 Level-2 dataset
- NDVI & NDBI index formulas

---

## 🤝 Acknowledgements

This project was developed by [Your Name] as a machine learning and remote sensing application using Google Earth Engine.

---

## 📢 License

MIT License
