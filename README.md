# 🏘️ Iran Housing Market Intelligence *(Ongoing)*

An end-to-end data pipeline analyzing Iran's real estate market using the full Divar (`divarofficial/real_estate_ads`) listings dataset — covering data cleaning, price normalization, spatial analysis, market scoring, price prediction, and ad-text signal analysis.

> ⚠️ **Status: Work in progress.** This project is under active development. The sections below reflect what has been completed so far; some components are still being refined and results are preliminary.

## 📋 Project Overview

This project builds a rigorous, reproducible pipeline for analyzing the Iranian housing market from real-world classified ad data. Rather than a single quick analysis, the focus is on **methodological soundness** — validating assumptions, testing sensitivity to modeling choices, and quantifying uncertainty — before drawing conclusions about pricing, market dynamics, and geography.

## 🗂️ Data Source

- Full **Divar real estate listings** dataset (`divarofficial/real_estate_ads`, via Hugging Face `datasets`)
- Supplementary **Iranian administrative boundary GeoJSON** data (provinces, counties/OSM boundaries) for spatial analysis

## 🧹 Part 1: Preprocessing & EDA (`Preprocessing_and_EDA.ipynb`)

- Loaded the full raw dataset and built a column-level profiling report (dtype, missingness, cardinality, memory usage)
- Explored distributions of numeric fields and validated which "should-be-numeric" columns actually coerce cleanly
- Flagged listings with coordinates falling outside Iran's geographic bounding box
- Filtered to the second half of 2024 (Jul–Dec) as the analysis window
- Standardized missing values, Persian text/characters, and boolean fields across columns
- Built a price-regime classification (sale / deposit+rent / full deposit / rent-only / daily rent) and determined currency based on evidence in the data
- Detected fake/inconsistent price values and set minimum plausible prices per regime
- Validated land/building size fields and flagged listings with invalid area values
- Identified negotiable ("توافقی") pricing patterns from ad descriptions
- Checked for duplicate rows/columns and suspicious repeated coordinate pairs
- Performed memory optimization and column-by-column missing-value cleanup

## 📊 Part 2: Market Analysis (`Market_Analysis.ipynb`)

### Rent-to-Deposit Conversion
- Derived an empirical "implied conversion rate" (monthly rent per million toman of deposit) from listings with both values present
- Tested multiple conversion scenarios (20k–45k) and measured how sensitive results are to the chosen rate

### Price Normalization
- Built a standardized `price per sqm` / `equivalent monthly rent` metric across all price regimes (sale, deposit+rent, full deposit, rent-only, daily rent)
- Applied outlier detection and removal per regime, with a documented sensitivity analysis comparing results before/after outlier removal

### Neighborhood & City Market Ranking
- Tested how minimum sample-size thresholds (10 to 3,000 listings) affect neighborhood price rankings, using Spearman correlation and top-N overlap to find a stable threshold
- Built a reliability-flagged neighborhood price summary (median/percentile price per sqm, missing rate, outlier rate) for Tehran, Mashhad, Karaj, and Isfahan
- Ranked most/least expensive reliable neighborhoods per city

### Spatial Analysis
- Spatially joined listings to Iranian county (shahrestan) boundaries using OSM/GeoJSON data, with match-rate diagnostics and city↔county consistency checks
- Investigated coordinate precision and rounding patterns to detect low-quality/placeholder GPS points
- Built county-level choropleth maps and hexbin density maps of listing volume

### City Market Scoring
- Constructed a composite "market score" per city from listing growth, price growth, and price stability (coefficient of variation), weighted and scaled with `MinMaxScaler`
- Applied a sample-size confidence adjustment to avoid overweighting thin markets
- Tested multiple weighting scenarios (balanced / price-focused / supply-focused) and built a robustness-averaged city ranking to identify consistently "hot" and "cold" markets

### Price Prediction Model
- Engineered features (amenities, room count, floor count, building/land size, construction year, location) for a residential sale-price model
- Established a naive median-baseline MAE for comparison
- Built a temporal train/test split (respecting listing chronology) and trained a `Ridge` regression pipeline (One-Hot + median/mode imputation)
- Evaluated MAE, RMSE, and R², with error broken down by city, property type, and building size group to identify where the model over/underperforms

### Ad Text Signal Analysis
- Extracted regex-based signals from ad titles/descriptions (e.g. "new construction," "never lived in," "urgent sale") 
- Measured each signal's frequency and tested its statistical association with price using controlled group comparisons

## 🛠️ Tools Used

- Python: `pandas`, `numpy`, `matplotlib`, `seaborn`
- `geopandas`, `shapely` for spatial analysis
- `scikit-learn` (`Ridge`, `ColumnTransformer`, `MinMaxScaler`) for modeling
- `scipy.stats` for statistical testing
- Hugging Face `datasets` for data loading
- Persian text rendering: `arabic_reshaper`, `python-bidi`

## 🚧 Still In Progress

- Finalizing and validating the price prediction model (feature refinement, potential non-linear models)
- Completing the ad-text signal significance analysis and conclusions
- Consolidating outputs into a final report / dashboard
- Documentation and reproducibility polish (requirements file, pipeline entry point)

## 📁 Repository Structure (so far)

- `Preprocessing_and_EDA.ipynb` — data loading, cleaning, and exploratory analysis
- `Market_Analysis.ipynb` — price normalization, spatial analysis, market scoring, prediction modeling, text signal analysis

## 📬 Contact

**Negar Yeganeh**  
[GitHub](https://github.com/NegarYS) · [LinkedIn](https://linkedin.com/in/negar-yeganeh)
