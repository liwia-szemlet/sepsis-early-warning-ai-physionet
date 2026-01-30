# Exploratory Sepsis Risk Modeling Using ICU Time Series Data

<div align="justify">
This repository presents an exploratory machine learning pipeline for modeling sepsis risk using ICU time series data. The project is based on the PhysioNet Computing in Cardiology Challenge 2019 dataset and focuses on feature engineering, class imbalance handling, and performance evaluation under highly imbalanced clinical conditions.
</div>

[View Notebook on NBViewer](https://nbviewer.org/github/liwia-szemlet/sepsis-early-warning-ai-physionet/blob/main/notebooks/sepsis_prediction_analysis.ipynb)

---

## Citations and Data Sources

<div align="justify">
Reyna MA, Josef CS, Jeter R, Shashikumar SP, Westover MB, Nemati S, Clifford GD, Sharma A. Early Prediction of Sepsis From Clinical Data: The PhysioNet/Computing in Cardiology Challenge. Critical Care Medicine 48 2: 210-217 (2019). https://doi.org/10.1097/CCM.0000000000004145
<br><br>
Reyna, M., Josef, C., Jeter, R., Shashikumar, S., Moody, B., Westover, M. B., Sharma, A., Nemati, S., & Clifford, G. D. (2019). Early Prediction of Sepsis from Clinical Data: The PhysioNet/Computing in Cardiology Challenge 2019 (version 1.0.0). PhysioNet. RRID:SCR_007345. https://doi.org/10.13026/v64v-d857
<br><br>
Goldberger, A., Amaral, L., Glass, L., Hausdorff, J., Ivanov, P. C., Mark, R., ... & Stanley, H. E. (2000). PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals. Circulation [Online]. 101 (23), pp. e215–e220. RRID:SCR_007345.
</div>

---

## Technical Stack and Architecture

* **Cloud Infrastructure**: AWS S3 utilized for data lake storage and high throughput ingestion of physiological records.
* **Programming Environment**: Python 3.9 plus environment leveraging vectorized operations via NumPy.
* **Data Orchestration**: Pandas for complex time series alignment and multi index patient record management.
* **Modeling Engine**: XGBoost (Extreme Gradient Boosting) selected for its robust handling of sparse clinical matrices and native L2 regularization.
* **Optimization Strategy**: Gradient reweighting via scale_pos_weight to mitigate extreme label imbalance (~1.8% positive class prevalence).

---

## Methodological Framework

### Data Ingestion and Imputation Strategy
<div align="justify">
The pipeline addresses extreme clinical sparsity through a systematic approach to missing data. Medical sensor readings are inherently irregular, necessitating a strategy that maintains physiological continuity without introducing bias. Forward fill (ffill) was applied to propagate the last observed measurement forward in time for baseline imputation. This approach is used as a simplified baseline strategy for handling irregular ICU measurements and is not intended to represent true physiological state estimation, while Back Fill (bfill) is utilized for initial timestamps. This approach mirrors a logic where a patient baseline remains the most reliable estimate until updated by a practitioner.
</div>

### Advanced Feature Engineering
<div align="justify">
To capture the dynamic nature of patient deterioration, raw data was transformed into high order clinical indicators. A derived hemodynamic ratio feature inspired by the Shock Index (HR / SBP) was included as an engineered input variable. This feature was used as a heuristic physiological proxy rather than a validated clinical decision marker. Vitals Delta measures the rate of change over 3 hour windows to isolate acute physiological instability. Additionally, 6 hour rolling averages were used to distinguish between transient sensor noise and persistent trends.
</div>

---

## Results Interpretation and Visual Analysis

### 1. Clinical Decision Factors (XGBoost Feature Gain)
**Path**: `plots/clinical_decision_factors.png`

<div align="justify">
This visualization ranks features by Information Gain, identifying the variables most critical for risk stratification. ICULOS (ICU Length of Stay index) emerged as a high-importance temporal feature, reflecting time-dependent patterns in the dataset. This behavior highlights known temporal bias effects in ICU datasets and is treated as a baseline time proxy rather than a causal clinical predictor. The significance of the engineered Shock Index high in the ranking validates the decision to integrate hemodynamic ratios, as the model prioritizes these composite markers over raw heart rate or blood pressure readings.
</div>


### 2. Model Performance: Precision Recall Curve
**Path**: `plots/precision_recall_curve.png`

<div align="justify">
In imbalanced clinical settings, the Precision Recall curve is the primary instrument for assessing performance. The model was optimized to increase recall under extreme class imbalance, resulting in moderate sensitivity (approximately 0.46) with a tradeoff in precision. This reflects the typical performance compromise observed in early-stage sepsis risk modeling.
This ensures the system flags a significant portion of the septic population for immediate clinical review.
</div>


### 3. Comprehensive Clinical Feature Correlation (Sets A+B)
**Path**: `plots/clinical_correlation_matrix.png`

<div align="justify">
This triangular heatmap visualizes Pearson correlation coefficients across the full dataset of 40 variables. It identifies clusters of physiological co variation, such as the relationship between laboratory markers like Bilirubin and Creatinine. Observed correlations highlight statistical co-variation patterns between laboratory markers. These relationships are exploratory and non-causal, serving as descriptive analysis. The bottom row maps each variable directly to the SepsisLabel, providing a baseline statistical association for the gradient boosting engine.
</div>

### 4. Primary Clinical Correlation Analysis
**Path**: `plots/correlation_matrix.png`

<div align="justify">
This matrix identifies linear dependencies specifically among the core vital signs and demographic markers. It is used to monitor for multicollinearity between parameters like MAP (Mean Arterial Pressure) and SBP (Systolic Blood Pressure). These identified dependencies are managed by the L2 regularization parameters within the XGBoost algorithm to prevent model overfitting.
</div>

---

## Project Scope and Limitations

This project demonstrates an experimental offline risk modeling pipeline using retrospective ICU data. The system is not intended for real-time clinical deployment and does not provide medical decision support. The results illustrate typical challenges in sepsis prediction, including extreme class imbalance, temporal bias, and performance tradeoffs between sensitivity and precision.
The repository is intended for educational and portfolio purposes, focusing on applied machine learning techniques in healthcare datasets.
