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

## 📁 Repository Structure

