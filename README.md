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

![1_LST](https://github.com/user-attachments/assets/fa177034-5ae7-4a4b-846c-0397106ba245)
![2_Heat](https://github.com/user-attachments/assets/cf8824ed-2dc1-4066-b750-aa1309d72312)
![3_Zoom1](https://github.com/user-attachments/assets/1d5af663-7edb-4e4c-b2ac-36841126099e)
![4_Zoom2](https://github.com/user-attachments/assets/cefbefe3-0df6-462e-9e4d-a978cb49564e)


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


![image](https://github.com/user-attachments/assets/d54e08ab-c7b4-44e4-8d0f-b66b61fcb096)

![image](https://github.com/user-attachments/assets/a910db7e-8125-44e8-ad7f-4bf361660167)



(Note: Actual numbers may vary depending on study area and sample selection.)

---


---

## ✨ Results Interpretation

✅ The classified heat stress zones reveal **spatial variations in heat exposure across the urban landscape**.

Key findings:

- **Low heat stress (green zones)** are concentrated in vegetated and peri-urban areas, benefiting from vegetation cooling.
- **Medium to high heat stress (yellow/orange zones)** dominate built-up urban cores, where impervious surfaces reduce evapotranspiration.
- **Extreme heat stress zones (red)** cluster in densely built, industrial, or low-vegetation zones, representing potential urban heat island hotspots.

👉 The map highlights where **urban cooling interventions (e.g., green roofs, tree planting)** could be prioritized to mitigate heat stress.

---

## ✨ How to Reproduce

1. Open [Google Earth Engine Code Editor](https://code.earthengine.google.com/).
2. Create a new script and copy the contents of `code/uhi_mapping_ml.js` into it.
3. Adjust:
 - `aoi` (Area of Interest) coordinates
 - Date range (e.g., summer months)
 - Classification thresholds if needed
4. Run the script.
5. View:
 - Land Surface Temperature map
 - Machine-learning-classified heat stress map
 - Optional: export results as GeoTIFF or CSV

If exporting, see `code/export_instructions.md` for guidance.

---

## 💡 Applications

✅ Identifying urban heat islands  
✅ Targeting areas for cooling infrastructure  
✅ Supporting climate adaptation and heat resilience planning

---

## 📝 References

- Google Earth Engine Documentation: https://developers.google.com/earth-engine
- Landsat 8 Collection 2 Level-2 dataset
- NDVI & NDBI index formulas

---

## 🤝 Acknowledgements

This project was developed by Konlavach Mengsuwan as a machine learning and remote sensing application using Google Earth Engine.

---

## 📢 License

MIT License

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

```
// 1️⃣ Add input variables (NDVI, NDBI) as extra bands
var ndvi = landsat.normalizedDifference(['SR_B5', 'SR_B4']).rename('NDVI');
var ndbi = landsat.normalizedDifference(['SR_B6', 'SR_B5']).rename('NDBI');

var input = lst.addBands(ndvi).addBands(ndbi);

// 2️⃣ Create labeled heat stress zones from LST
var low = lst.lt(28).selfMask().rename('class').int().multiply(0);
var medium = lst.gte(28).and(lst.lt(32)).selfMask().rename('class').int().multiply(1);
var high = lst.gte(32).and(lst.lt(36)).selfMask().rename('class').int().multiply(2);
var extreme = lst.gte(36).selfMask().rename('class').int().multiply(3);

var classImage = low.unmask().add(medium.unmask()).add(high.unmask()).add(extreme.unmask());

// 3️⃣ Sample points from image
var trainingPoints = input.addBands(classImage).stratifiedSample({
  numPoints: 100,  // per class
  classBand: 'class',
  region: aoi,
  scale: 30,
  seed: 42,
  geometries: true
});

// 4️⃣ Train Random Forest classifier
var classifier = ee.Classifier.smileRandomForest(50).train({
  features: trainingPoints,
  classProperty: 'class',
  inputProperties: input.bandNames()
});

// 5️⃣ Apply classifier
var classified = input.classify(classifier);

// 6️⃣ Visualize classified map
var palette = ['green', 'yellow', 'orange', 'red'];
Map.addLayer(classified, {min: 0, max: 3, palette: palette}, 'Heat Stress Zones');

```

## 📢 License

MIT License
