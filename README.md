# CAIE — Beam Bottom Bar Prediction (Traditional ML)

**Goal:** Predict the **required bottom reinforcement area (As, mm²)** for RC beams from  
**Width (mm), Depth (mm), Sagging Moment (kNm), Fck (MPa)** using traditional ML.  
This forms **Part 1** of the CAIE final project; Part 2 integrates an LLM to propose bar sets (e.g., `3T12`, `2T16`) from the predicted As.

---

## Dataset & Features
- Source: Beam audit exported from CSC Tedds → `df_Opt_BotBar_cleaned.csv`
- Features: `Width_mm`, `Depth_mm`, `Moment_kNm`, `Fck`
- Target: `Req_BotBar_Area_mm2`
- **Training domain (after cleaning, n = 495):**
  - Width: **125–300 mm**
  - Depth: **450–600 mm**
  - Moment: **2–318 kNm**
  - Fck: **25 MPa** (constant in this dataset)

> ⚠️ The model is **reliable within the ranges above**. Inputs outside this scope should be retrained/expanded with additional data.

---

## Model Training
- Split: **80/20** (train/test)
- Algorithms (traditional ML): **Linear Regression**, **Ridge Regression**, **Random Forest Regressor (n=300)**
- Metrics: **R²**, **MAE (mm²)**, **RMSE (mm²)**

### Results (Test Set)
| Model                | R²     | MAE (mm²) | RMSE (mm²) |
|----------------------|:------:|----------:|-----------:|
| **RandomForest(n=300)** | **0.851** | **44.32**   | **86.57**    |
| Ridge(alpha=1.0)     | 0.677  | 67.34     | 127.57     |
| LinearRegression     | 0.677  | 67.23     | 127.64     |

**Interpretation**
- **R² = 0.851** → model explains ~85% of variance in required As on unseen data.
- **MAE ≈ 44 mm²** → about **0.39 × T12** (T12 ≈ 113 mm²), typically absorbed when rounding up to discrete bar sets.
- **Selected model for deployment:** **Random Forest** (best overall metrics).

---

## Reproducibility (Google Colab)
Use this raw CSV in Colab:
