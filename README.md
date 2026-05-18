# Aquaculture-Mapping-Using-Sentinel-1-SAR-using-Google-Earth-Engine
Designedand implementeda remote sensing workflow for aquaculture and waterbody mapping using Sentinel-1 SAR imagery, raster analysis, SDWI, and temporal filtering techniques in Google Earth Engine.

# This project performs stable aquaculture pond mapping using multi-temporal Google Earth Engine and Copernicus Programme Sentinel-1 SAR data.It combines SAR backscatter, SDWI, terrain filtering, texture analysis, temporal persistence, and geometric filtering to identify stable aquaculture ponds over one year.

# workflow


# 1. Area of Interest (AOI) Preparation

Load AOI boundary from Earth Engine assets.
Convert feature collection into geometry.
Simplify geometry for faster processing.
Set map visualization and satellite basemap.

# Purpose

# Defines the geographic region where aquaculture mapping is performed.

# 2. Sentinel-1 SAR Data Collection
Load Sentinel-1 GRD imagery.
Filter by:
Date range (last 12 months)
IW mode
10 m resolution
VV and VH polarization
Clip imagery to AOI.

# Purpose

SAR imagery enables water detection even under cloud cover.

# 3. SAR Composite Generation
Create median composite from yearly Sentinel-1 collection.
Extract:
VV band
VH band

# Purpose

Reduces speckle noise and creates a stable annual representation.

# 4. SDWI Calculation

The SAR-derived water index is computed using:

SDWI=(VV+VH)×10

# Purpose

Enhances separation between water and non-water surfaces.

# 5. Initial Water Extraction

Water is detected using dual-threshold conditions:

SDWI < -430
VV < -17 dB
Logic
Water Pixel =
(SDWI < Threshold)
AND
(VV < Threshold)
Purpose

Combines spectral and backscatter characteristics for reliable water detection.

# 6. Terrain Filtering
Load SRTM DEM.
Generate slope layer.
Keep only flat regions:
Slope < 5°

# Purpose

Removes hill shadows and terrain-induced SAR artifacts.

# 7. GLCM Texture Analysis

Convert VV into integer scale.
Compute GLCM texture metrics.
Extract entropy layer.
Texture Rule
Entropy < 3
Purpose

Aquaculture ponds usually have smooth and homogeneous texture.

# 8. Morphological Cleaning

Apply:

focal_min()
focal_max()
Purpose

Removes isolated noisy pixels and smooths water boundaries.

# 9. Majority Filtering

Apply focal mode filtering:

focal_mode({
  radius: 1,
  iterations: 2
})
Purpose

Fills small gaps and improves spatial continuity.

# 10. Connected Pixel Filtering
Calculate connected pixel count.
Remove small isolated patches.
Criteria
Connected Pixels ≥ 30

# Purpose

Removes noise and retains significant water bodies.

# 11. Vectorization
Convert raster water mask into polygons.
Reproject to EPSG:4326.
Use reduceToVectors().

# Purpose

Creates vector features for geometric analysis.

# 12. Geometric Feature Extraction

For each polygon calculate:

Area

Perimeter

Perimeter

Compactness

Compactness=
P
2
4πA
	​


Rectangularity

Rectangularity=
BoundingBoxArea
Area
	​


# Purpose

Aquaculture ponds typically exhibit rectangular and compact geometry.

# 13. Aquaculture Pond Identification

Filter polygons using geometric thresholds.

Criteria
Parameter	Threshold
Area	500 – 300000 m²
Rectangularity	> 0.35
Compactness	> 0.05

# Purpose

Separates aquaculture ponds from rivers, lakes, and irregular wetlands.

# 14. Monthly Water Mapping

For each of the 12 months:

Create monthly Sentinel-1 composite.
Compute SDWI.
Apply water thresholds.
Apply terrain mask.

# Purpose

Tracks temporal persistence of water.

# 15. Water Persistence Analysis

Compute water occurrence frequency:

Water Frequency =
Sum of Monthly Water Masks
Stable Water Rule
Water Present ≥ 10 Months

# Purpose

Identifies permanent or semi-permanent water bodies.

# 16. Stable Water Cleaning

Apply:

Majority filtering
Connected pixel filtering
Criteria
Connected Pixels ≥ 20

# Purpose

Removes unstable seasonal noise.

# 17. Stable Aquaculture Extraction

Stable water polygons are filtered again using:

Parameter	Threshold
Area	1000 – 300000 m²
Rectangularity	> 0.35
Compactness	> 0.05

# Purpose

Extracts long-term operational aquaculture ponds.

# 18. Export Outputs
Vector Export
Stable aquaculture ponds as SHP.
Raster Export
Stable water mask raster.

# Purpose

Provides GIS-ready outputs for analysis and reporting.

# Final Outputs

The workflow generates:

Annual water mask
Stable water layer
Aquaculture pond polygons
Stable aquaculture layer
Water persistence map

# Technologies Used

Google Earth Engine
Copernicus Programme Sentinel-1 SAR
SRTM DEM
GLCM Texture Analysis
Morphological Image Processing
Connected Component Analysis

# # ## Google Earth Engine Script

https://code.earthengine.google.com/b6e5d43311e2b44c16aa0a00f196f9c7

