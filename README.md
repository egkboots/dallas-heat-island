# Dallas Urban Heat Island Analysis

**Geospatial Data Science | Machine Learning | Public Health Equity**

An end-to-end data science project analyzing urban heat inequality across 645 census tracts in Dallas County, TX using NASA Landsat satellite imagery, U.S. Census data, and EPA EnviroAtlas. Includes data cleaning, exploratory analysis, XGBoost modeling with LIME explainability, and an interactive Tableau dashboard.  
[View Interactive Dashboard](https://public.tableau.com/app/profile/eleanor.koo/viz/DallasHeatProject/DallasUrbanHeatIslandAnalysisDallasCountyTXJuly2023?publish=yes)

## Key Findings

- **36% of Dallas census tracts** face extreme heat risk (surface temp \>44°C / 111°F) in summer  
- **Low-income tracts are disproportionately hotter** — extreme heat tracts cluster heavily below $75,000 median income  
- **Vegetation is the strongest cooling factor** — tracts with NDVI \>0.22 are \~23% cooler than tracts below 0.15  
- **Median income correlates with tree canopy** (r=0.27), quantifying the environmental cost of inequality  
- XGBoost model achieved **R²=0.908** and **RMSE=2.51°C** on held-out test data

## Project Structure

dallas-heat-island/

*  notebooks/  
  * 01\_data\_loading.ipynb       \# Load all datasets  
  * 02\_data\_cleaning.ipynb      \# Clean, transform, engineer features  
  * 03\_eda.ipynb                \# Exploratory analysis and visualizations  
  * 04\_modeling.ipynb           \# XGBoost, Random Forest, LIME explanations  
* data/  
  * raw/                        \# Source data (not tracked in Git)  
  * processed/                  \# Cleaned intermediate files  
  * outputs/                    \# Final exports for Tableau  
* README.md

## Data Sources

| Dataset | Source | Description |
| :---- | :---- | :---- |
| Landsat 8 OLI/TIRS C2 L2 | NASA / USGS EarthExplorer | Land Surface Temperature (ST\_B10), NDVI bands (SR\_B4, SR\_B5) |
| EnviroAtlas CONUS Metrics | U.S. EPA | % Impervious surface, % Tree canopy by HUC-12 watershed |
| ACS 5-Year Estimates | U.S. Census Bureau | Median income (B19013), Population (B01003), Housing age (B25034) |
| TIGER/Line Shapefiles | U.S. Census Bureau | Census tract boundaries for Dallas County, TX |

---

## Methods

### Data Processing

- Loaded Landsat GeoTIFF bands using rasterio and applied USGS scale factor to convert raw digital numbers to land surface temperature in Celsius: LST \= DN × 0.00341802 \+ 149.0 − 273.15  
- Calculated NDVI from red (B4) and near-infrared (B5) bands: NDVI \= (B5 − B4) / (B5 \+ B4)  
- Reprojected census tract shapefiles from EPSG:4269 to EPSG:32614 (UTM Zone 14N) to match Landsat CRS  
- Performed zonal statistics using rasterstats to aggregate mean LST and NDVI per census tract  
- Merged five datasets on census tract GEOID, resolving multi-header CSVs, coordinate mismatches, and missing values

### Feature Engineering

| Feature | Source | Description |
| :---- | :---- | :---- |
| lst\_celsius | Landsat ST\_B10 | Mean land surface temperature per tract |
| ndvi\_mean | Landsat SR\_B4/B5 | Mean vegetation index per tract |
| median\_income | Census ACS B19013 | Median household income |
| total\_population | Census ACS B01003 | Total tract population |
| pct\_old\_housing | Census ACS B25034 | % housing units built before 1980 |
| area\_sqkm | TIGER Shapefile | Tract land area in km² |
| intptlat / intptlon | TIGER Shapefile | Tract centroid coordinates |

### Modeling

* Trained and compared **Random Forest** and **XGBoost** regressors on an 80/20 train/test split (504 train, 126 test)  
* Selected XGBoost as final model based on R²=0.908 vs 0.888 and RMSE=2.51°C vs 2.78°C  
* Applied **LIME** (Local Interpretable Model-agnostic Explanations) to explain individual tract predictions, revealing geographic location as the dominant heat driver (\>90% combined feature importance)  
* Classified tracts into four heat risk tiers based on predicted LST: Low (\<30°C), Moderate (30–38°C), High (38–44°C), Extreme (\>44°C)

### Model Performance

| Model | R² | RMSE (°C) | MAE (°C) |
| :---- | :---- | :---- | :---- |
| Random Forest | 0.888 | 2.78 | 1.92 |
| **XGBoost** | **0.908** | **2.51** | **1.93** |

## Dashboard

The interactive Tableau dashboard allows users to:

* Explore surface temperature across all 645 Dallas census tracts on a choropleth map  
* Filter by heat risk tier to see how income, vegetation, and housing age differ across risk groups  
* Examine scatter plots of temperature vs. income and temperature vs. NDVI  
* View average tract profiles by heat risk tier

[View on Tableau Public](https://public.tableau.com/app/profile/eleanor.koo/viz/DallasHeatProject/DallasUrbanHeatIslandAnalysisDallasCountyTXJuly2023?publish=yes)

## Tech Stack

| Category | Tools |
| :---- | :---- |
| Languages | Python 3.13 |
| Data Processing | pandas, NumPy, GeoPandas |
| Geospatial | rasterio, rasterstats, pyogrio |
| Modeling | scikit-learn, XGBoost, LIME |
| Visualization | Matplotlib, Seaborn, Tableau Public |
| Environment | VS Code, Jupyter Notebooks, Git |

---

## How to Run

1. Clone the repository

git clone https://github.com/egkboots/dallas-heat-island.git

cd dallas-heat-island

2. Create and activate virtual environment

python \-m venv venv

venv\\Scripts\\activate  \# Windows

3. Install dependencies

pip install numpy pandas geopandas rasterio rasterstats scikit-learn xgboost matplotlib seaborn jupyter plotly lime

4. Download data (see Data Sources above) and place in data/raw/  
     
5. Run notebooks in order: 01\_data\_loading → 02\_data\_cleaning → 03\_eda → 04\_modeling

## Author

**Eleanor Koo** — Undergraduate Data Science, UT Austin  
[GitHub](https://github.com/egkboots) | [LinkedIn](https://www.linkedin.com/in/eleanor-koo) | [eleanor.koo16@gmail.com](mailto:eleanor.koo16@gmail.com)  
