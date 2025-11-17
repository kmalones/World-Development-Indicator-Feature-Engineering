# World-Development-Indicator-Feature-Engineering
# World Development Indicator — Feature Engineering & Classification

This repository contains a Python script (originally built in Google Colab) that prepares World Bank-style development indicators, performs a small feature engineering step, and runs a logistic regression model to predict whether a country-year observation is "high population" (binary target). The notebook/script demonstrates data reshaping (long → wide), simple target creation, basic cleaning, model training, and evaluation (ROC, confusion matrix, cumulative gain).

> Note: The primary script in this repository is `world_development_indicator.py` (originally a Colab notebook). The script expects the dataset `worlddata.csv` to be available in Google Drive when run in Colab, but can be adjusted for local runs.

## Table of contents

- Overview
- Files
- Dependencies
- Data
- How the script works
- How to run
- Outputs produced
- Known issues & recommendations
- Suggested improvements
- License
- Contact

## Overview

The script performs the following high-level steps:

1. Mounts Google Drive and reads `/content/drive/MyDrive/BUDT704/Exercises/LDatasets/worlddata.csv`.
2. Filters observations between 2000 and 2015.
3. Identifies indicators with the most non-missing observations and selects a subset of indicators for modeling:
   - Population, total
   - Internet users (per 100 people)
   - Mortality rate, infant (per 1,000 live births)
   - Population density (people per sq. km of land area)
   - Mobile cellular subscriptions (per 100 people)
4. Pivots the data to a wide format and standardizes column names.
5. Creates a binary target `Is_High_Pop` using the median population as threshold.
6. Drops rows with missing values in selected features.
7. Trains a logistic regression model to predict `Is_High_Pop`.
8. Computes and prints evaluation metrics and model coefficients. Plots ROC and cumulative gain charts.

## Files

- world_development_indicator.py — main script (Colab export) that executes the full pipeline.
- (Expected) worlddata_clean_wide.csv — saved cleaned wide-form dataset created by the script (written to the working directory).

## Dependencies

The script uses common data science packages. Install the required packages with:

- python >= 3.7
- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn
- (if run in Colab) google.colab
- (if you want to display saved images locally) IPython

Example pip command:
```
pip install pandas numpy matplotlib seaborn scikit-learn
```

If you intend to run the script in Google Colab, the `google.colab` package and a Google account with access to the Drive file are required.

## Data

The script expects a CSV file named `worlddata.csv` in Google Drive at:
`/content/drive/MyDrive/BUDT704/Exercises/LDatasets/worlddata.csv`

The original dataset should be in long format with at least the following columns:
- CountryName
- CountryCode
- IndicatorName
- Year
- Value

If you want to run locally, place `worlddata.csv` in the same folder as the script or modify the path in the script to point to your dataset location.

## How the script works (important notes)

- The script is written for Google Colab and includes:
  - drive.mount('/content/drive') — mounts Google Drive.
  - `display(Image(filename='...'))` — expected to display saved PNGs (requires IPython.display import when running locally).
- It selects 5 indicators, pivots to wide format, creates `Is_High_Pop` (1 when population > median population), drops missing rows, then trains and evaluates a logistic regression model.
- The script prints evaluation metrics and writes `worlddata_clean_wide.csv` to the working directory.

## How to run

Two common ways:

1) Run in Google Colab (recommended, minimal edits):
- Upload the script to Colab or paste the code into a Colab notebook cell.
- Ensure `worlddata.csv` is in the expected Google Drive path.
- Run the notebook cells; follow the Drive authorization steps.

2) Run locally:
- Remove or comment out the Colab-specific lines:
  - Remove or modify `from google.colab import drive` and `drive.mount('/content/drive')`.
  - Change the CSV path to a local path, e.g. `df = pd.read_csv('path/to/worlddata.csv')`.
- Ensure you have IPython if you want to use `display(Image(...))`:
  - Add `from IPython.display import Image, display` if displaying images.
- Execute:
```
python world_development_indicator.py
```

## Outputs produced

- worlddata_clean_wide.csv — cleaned wide dataset saved by the script.
- Printed outputs:
  - Top indicators by completeness (count of non-missing observations).
  - Final dataset shape and head.
  - Model evaluation metrics (AUC, confusion matrix, accuracy).
  - Table of logistic regression coefficients and odds ratios.
- Plots (in-memory or displayed):
  - ROC curve
  - Cumulative gain chart

Note: The current script prints messages claiming saved plots (e.g., 'roc_curve.png', 'cumulative_gain_chart.png') but the plotting code does not save these files to disk. If you need saved image files, uncomment or add plt.savefig(...) calls before plt.close().

Example to save ROC plot:
```python
plt.savefig('roc_curve.png', bbox_inches='tight')
```

And add similarly to the cumulative gain chart function.

## Known issues & recommendations

- The script uses Colab-specific imports and drive mounting — update paths for local runs.
- Column renaming section uses manual names; changing the chosen indicators or source wording may break the renaming mapping. Consider using a mapping dictionary that is generated from cleaned indicator names.
- The FEATURES list contains a column name `'Population_density_people_per_sq_km_of_land_area_'` which is inconsistent with the renaming earlier in the script. Ensure FEATURES matches the wide dataframe column names after renaming.
- The script prints display(Image(...)) calls but does not import Image or save images by default. Add `from IPython.display import Image, display` and `plt.savefig(...)` if running outside interactive environments.
- The script drops any rows with missing values for the selected indicators. You might prefer imputation for larger data retention.


## License

Use or adapt as needed. Add an appropriate license file (e.g., MIT) to the repository if you want to make it open-source.

