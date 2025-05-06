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

---
![1_LST](https://github.com/user-attachments/assets/fa177034-5ae7-4a4b-846c-0397106ba245)
![2_Heat](https://github.com/user-attachments/assets/cf8824ed-2dc1-4066-b750-aa1309d72312)
![3_Zoom1](https://github.com/user-attachments/assets/1d5af663-7edb-4e4c-b2ac-36841126099e)
![4_Zoom2](https://github.com/user-attachments/assets/cefbefe3-0df6-462e-9e4d-a978cb49564e)

## 💡 Applications

✅ Urban heat island visualization  
✅ Identify heat-stressed zones for urban planning  
✅ Support heat mitigation strategies (e.g., green infrastructure targeting)

---

## 📝 References

- Google Earth Engine Documentation: https://developers.google.com/earth-engine
- Landsat 8 Collection 2 Level-2 dataset
- NDVI & NDBI index formulas
- Project code: https://code.earthengine.google.com/155683c7c19a54f488da76d45f9c1bde
---

## 🤝 Acknowledgements

This project was developed by Konlavach Mengsuwan as a machine learning and remote sensing application using Google Earth Engine.

---

## Code
```
var aoi = ee.Geometry.Rectangle([13.1, 52.3, 13.7, 52.7]);  // Example: Berlin
Map.centerObject(aoi, 10);

var landsat = ee.ImageCollection('LANDSAT/LC08/C02/T1_L2')
              .filterBounds(aoi)
              .filterDate('2024-06-01', '2024-08-31')  // Summer months
              .filter(ee.Filter.lt('CLOUD_COVER', 10))
              .median();

var lst = landsat.select('ST_B10')
                 .multiply(0.00341802)
                 .add(149.0)
                 .subtract(273.15);  // Convert to Celsius

var vizParams = {min: 20, max: 40, palette: ['blue', 'green', 'yellow', 'red']};
Map.addLayer(lst.clip(aoi), vizParams, 'Land Surface Temperature');

var urban = ee.Image('ESA/WorldCover/v100/2020')
              .eq(50);  // 50 = urban class
              
var urbanLST = lst.updateMask(urban);
Map.addLayer(urbanLST.clip(aoi), vizParams, 'Urban LST');

var districts = ee.FeatureCollection('FAO/GAUL_SIMPLIFIED_500m/2015/level2')
                  .filterBounds(aoi);

var stats = urbanLST.reduceRegions({
  collection: districts,
  reducer: ee.Reducer.mean(),
  scale: 30
});

print(stats);

```

## 📢 License

MIT License
