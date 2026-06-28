# Carbon Stock Mapping of JMI Campus Using Google Earth Engine

## Overview

A machine learning-based carbon stock estimation study for Jamia Millia Islamia (JMI)
campus, New Delhi, using multi-source satellite data and Google Earth Engine.
The study integrates SAR, optical, and LiDAR-derived canopy height data to estimate
above-ground biomass and carbon stocks across the urban campus.

---

## Tools & Technologies

- **Google Earth Engine (GEE)** — cloud-based geospatial processing
- **Sentinel-1 SAR** — VV/VH polarization for vegetation structure
- **Sentinel-2 Optical** — spectral bands, NDVI, EVI
- **GEDI LiDAR** — canopy height reference data (rh98)
- **SRTM DEM** — elevation and slope terrain features
- **Dynamic World** — land cover classification and vegetation masking
- **Random Forest Regression** — machine learning model (500 trees)

---

## Methodology

### Phase 1 — Data Collection & Preprocessing
- Loaded post-monsoon Sentinel-1 SAR data (Oct–Dec 2023) to avoid cloud contamination
- Computed percentile composites (25th, 50th, 75th) for robust SAR statistics
- Applied cloud masking on Sentinel-2 SR imagery
- Derived terrain variables (elevation, slope) from SRTM DEM
- Used Dynamic World to mask out buildings and roads from analysis

### Phase 2 — Feature Engineering
Combined 18 predictor variables:

| Category | Features |
|---|---|
| SAR (Sentinel-1) | VH, VV median + IQR texture |
| Optical (Sentinel-2) | B2–B12 spectral bands |
| Vegetation Indices | NDVI, EVI |
| Terrain | Elevation, Slope |

### Phase 3 — Machine Learning Model
- Trained **Random Forest Regression** (500 trees) using GEDI canopy height as reference
- Split data: 70% training / 30% testing
- Compared against Gradient Tree Boosting and CART models

### Phase 4 — Carbon Stock Estimation
- Converted predicted canopy height → Above-Ground Biomass (AGB) using allometric equations
- Applied IPCC default carbon fraction (0.47) to estimate carbon stocks
- Calculated total carbon storage in tonnes across the campus

---

## Key Results

- Generated wall-to-wall canopy height map at **10m resolution**
- Estimated Above-Ground Biomass (Mg/ha) across vegetated areas
- Calculated total carbon stock (tonnes) for JMI campus
- Produced exportable GeoTIFF outputs for canopy height, AGB, and carbon maps

---

## GEE Code

```javascript
// Sample: Carbon Stock Estimation from Canopy Height
// Using IPCC default carbon fraction (0.47)

var agb = canopyHeightMasked.expression(
  '10.0 * pow(height, 1.3)', {
    'height': canopyHeightMasked.select('canopy_height')
  }).rename('AGB_Mg_ha');

var carbonStock = agb.multiply(0.47).rename('Carbon_Mg_ha');
```

---

## Skills Demonstrated

- Multi-source satellite data integration and preprocessing
- Machine learning regression in Google Earth Engine
- SAR and optical remote sensing analysis
- Carbon stock estimation using allometric equations
- Geospatial data export and reporting