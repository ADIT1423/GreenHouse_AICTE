---

# 1. Green House gas emission prediction using random tree regressor

**Predicting Supply Chain Greenhouse Gas Emissions for US Industries**

# 2. Overview

This project implements a machine learning model to predict supply chain greenhouse gas (GHG) emission factors for various U.S. industries and commodities. The primary goal is to accurately estimate the `Supply Chain Emission Factors with Margins`—a crucial metric for understanding the environmental impact of products and services.

This predictive capability is vital for organizations aiming to conduct life-cycle assessments (LCAs), comply with environmental regulations, and develop sustainable supply chain strategies. By leveraging historical data, the model provides a scalable tool for estimating emissions where direct measurements are unavailable.

# 3. Motivation

In an era of increasing climate-related risks and regulatory scrutiny, businesses face growing pressure to monitor, report, and reduce their carbon footprint. Supply chain emissions (often categorized as Scope 3 emissions) represent a significant portion of a company's total environmental impact, yet they are notoriously difficult to measure.

This project was motivated by the need for a data-driven approach to fill these data gaps. A **Random Forest Regressor** was chosen for several key reasons:
- **Robustness:** It handles a mix of feature types and is less sensitive to outliers compared to linear models.
- **Performance:** It is a powerful ensemble method that often achieves high accuracy without extensive hyperparameter tuning.
- **Interpretability:** It provides mechanisms to evaluate feature importance, offering insights into which factors are the most significant drivers of emissions. This helps stakeholders understand the model's decisions and identify key areas for emission reduction efforts.

# 4. Repository Structure

```
.
├── data/
│   └── SupplyChainEmissionFactorsforUSIndustriesCommodities.xlsx
├── notebooks/
│   └── AICTE_Greenhouse_Prediction_2025_July-August.ipynb
├── final_model/
│   └── final_model.pkl
└── README.md
```

- **`data/`**: Contains the raw input dataset in Excel format.
- **`notebooks/`**: Jupyter notebooks used for initial data exploration, visualization, and prototyping.
- **`model/`**: Stores the serialized, trained machine learning model artifacts (e.g., using `joblib`).
- **`README.md`**: This file, providing a comprehensive overview of the project.

# 5. Data Description

The dataset is an Excel file, `SupplyChainEmissionFactorsforUSIndustriesCommodities.xlsx`, containing detailed GHG emission factors for U.S. industries and commodities from 2010 to 2016.

- **Data Sources:** Data is aggregated from two types of sheets for each year: `_Detail_Commodity` and `_Detail_Industry`.
- **Structure:** The script consolidates data from all years and sources into a single DataFrame.
- **Features:** The dataset includes identifiers, substance types (e.g., carbon dioxide, methane), and various emission factor measurements.
- **Missing Values:** The `Unnamed: 7` column was found to be entirely empty and was dropped. The `Margins of Supply Chain Emission Factors` column contained some missing values, which were imputed with zero.

### Key Features Used in the Model

| Feature Name | Description | Data Type (Post-Processing) |
| :--- | :--- | :--- |
| `Substance` | The type of greenhouse gas (e.g., carbon dioxide, methane). | Encoded Integer |
| `Unit` | The measurement unit for the emission factors. | Encoded Integer |
| `Supply Chain Emission Factors without Margins` | The core emission factor value, excluding supply chain margins. | Float |
| `Margins of Supply Chain Emission Factors` | The portion of emissions attributable to supply chain margins. | Float |
| `DQ ReliabilityScore ...` | Data Quality score for reliability. | Integer |
| `DQ TemporalCorrelation ...` | Data Quality score for temporal relevance. | Integer |
| `DQ GeographicalCorrelation ...`| Data Quality score for geographical relevance. | Integer |
| `DQ TechnologicalCorrelation ...`| Data Quality score for technological relevance. | Integer |
| `DQ DataCollection ...` | Data Quality score for data collection methods. | Integer |
| `Source` | Engineered feature indicating if the data is from 'Industry' or 'Commodity'. | Encoded Integer |
| `Supply Chain Emission Factors with Margins`| **Target Variable:** The total emission factor including margins. | Float |

# 6. Methodology

The end-to-end pipeline involves data preprocessing, feature engineering, and model training.

### 6.1 Data Preprocessing
1.  **Data Loading & Consolidation:** Data from `_Detail_Commodity` and `_Detail_Industry` sheets for each year (2010-2016) are loaded and concatenated into a unified pandas DataFrame.
2.  **Column Cleaning:** Extraneous columns (`Unnamed: 7`) and identifying columns not used for training (`Name`, `Code`, `Year`) are dropped. Column names are stripped of leading/trailing whitespace.
3.  **Categorical Encoding:** Categorical features are converted to numerical representations using dictionary mapping (a form of label encoding):
    - `Substance`: `{'carbon dioxide': 0, 'methane': 1, ...}`
    - `Unit`: `{'kg/2018 USD...': 0, 'kg CO2e/2018 USD...': 1}`
    - `Source`: `{'Commodity': 0, 'Industry': 1}`
4.  **Feature Scaling:** All features are normalized using `StandardScaler` to ensure they have a mean of 0 and a standard deviation of 1. This prevents features with larger scales from unduly influencing the model.

### 6.2 Feature Engineering
A new feature, `Source`, was created to preserve the distinction between data originating from the 'Commodity' sheets and the 'Industry' sheets. This allows the model to potentially learn different patterns associated with each data source.

### 6.3 Model Training
- **Model:** A `RandomForestRegressor` from `scikit-learn` is used.
- **Dataset Split:** The data is partitioned into a training set (80%) and a testing set (20%) with `random_state=42` to ensure reproducibility.
- **Training:** The model is trained on the scaled training feature set (`X_train`) and the corresponding target values (`y_train`). The current implementation uses the default hyperparameters of the `RandomForestRegressor`.

### 6.4 Hyperparameter Tuning
The current version of the script uses the default hyperparameters for the `RandomForestRegressor`. For future improvements, hyperparameter tuning using `GridSearchCV` or `RandomizedSearchCV` is recommended to optimize model performance. Key parameters to tune would include:
- `n_estimators`: The number of trees in the forest.
- `max_depth`: The maximum depth of each tree.
- `min_samples_split`: The minimum number of samples required to split an internal node.
- `min_samples_leaf`: The minimum number of samples required to be at a leaf node.

# 7. Results & Evaluation

The model's performance was evaluated on the held-out test set using standard regression metrics.

### Performance Metrics

| Metric | Test Set Score |
| :--- | :--- |
| **Root Mean Squared Error (RMSE)** | *[0.005948528382514106]* |
| **R² Score** | *[0.9993700440298772]* |

The R² score indicates the proportion of the variance in the target variable that is predictable from the features. An RMSE value close to zero signifies high predictive accuracy.

