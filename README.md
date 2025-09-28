## Introduction

This project investigates **how daily screen behaviors and sleep relate to self-reported mood (1–10)**, using a scale-aware, nonlinearity-aware analytics workflow. We analyze a daily, cross-sectional dataset with the following variables: `screen_time_hours`, `hours_on_TikTok`, `sleep_hours`, `social_media_platforms_used`, `stress_level (1–10 higher=worse)`, and `mood_score (1–10 higher=better)`.

### Methodology 
- **Data hygiene:** Schema standardization, type coercion (hours → float, scales → int), range/logic checks (0–24h; 1–10 scales; `TikTok ≤ total screen`), outlier flags (pre-registered), and a missingness audit (no meaningful missingness; MICE pipeline ready).
- **EDA (scale-aware):** Pearson *and* Spearman correlations; binned means; linear vs. spline nonlinearity checks.
- **Modeling plan (overfitting-aware):** Pre-registered hierarchy  
  **M1** sleep → **M2** +screen → **M3** +TikTok+platforms → **M4** +interactions (screen×sleep; share×sleep) → **M5** +splines (sleep & screen) + TikTok + platforms.  
  Metrics: **Adj-R², AIC/BIC, 5-fold CV: R², RMSE, MAE**.
- **Robustness:** Ordinal outcome check (Ordered Probit for mood 1–10); VIF for collinearity; bootstrap CIs for effects.
- **Exploratory mechanisms:** **Mediation** (screen → sleep → mood; bootstrapped indirect effect) and **moderation** (stress × screen; simple slopes).
- **Visualization standards:** Transparent scatter + LOWESS (stratified by stress and TikTok share), PDP/ICE from a simple gradient boosting regressor; 7–8h sleep band annotated.

### Models Used & Outputs
- **Primary regression model:** OLS with **B-splines** for `sleep_hours` and `screen_time_hours` (**M5**), plus `hours_on_TikTok` and `social_media_platforms_used`.  
  - **Out-of-sample performance (5-fold CV):** **CV R² ≈ 0.63**, **CV RMSE ≈ 0.78**, **CV MAE ≈ 0.59** on the 1–10 mood scale.  
  - **Info criteria:** M5 had the lowest AIC/BIC among the hierarchy.  
  - **Robustness:** An **Ordered Probit** yielded consistent signs/significance with the OLS/spline model.
- **Classification side-task (bonus):** Predict **high stress (≥8)** with a standard-scaled **Logistic Regression** (main effects + screen×sleep interaction).  
  - **ROC-AUC ≈ 0.936**, **PR-AUC ≈ 0.855**, **Brier ≈ 0.105** (good discrimination with reasonable calibration).

### Key Findings (brief)
- **Sleep ↔ Mood:** Strong positive, **nonlinear** association; benefits rise to **~7–8 hours** then plateau.  
- **Screen/TikTok ↔ Mood:** Negative associations; TikTok time adds a distinct negative effect beyond total screen time; platform count adds little.  
- **Nonlinearity matters:** Splines improved fit vs. linear models (CV R² ~0.63 vs. ~0.55).  
- **Mediation:** Indirect effect via sleep ≈ **0** (bootstrap CI spans 0) in this cross-section.  
- **Moderation:** At **higher stress**, the **screen→mood** slope is **more negative** (steeper decline).

### Limitations & Scope
- Cross-sectional and self-reported → findings are **associational, not causal**; unmeasured confounding may remain (e.g., baseline mental health, workload).
- Ratios can inflate VIF when combined with their components; we avoid such combinations in final models.

### Deliverables
- Reproducible Colab notebook with: data checks, EDA, model hierarchy + CV, effect sizes with 95% CIs (plus bootstrap), ordinal sensitivity, mediation/moderation, PDP/ICE visuals, and saved figures in `/content/figures/`.

**In sum:** A disciplined, scale-aware analysis shows that **sleep is the strongest correlate of better mood**, **screen (especially TikTok) relates to worse mood**, **stress amplifies** the negative screen effect, and **nonlinearity** is essential to model these relationships accurately. The best model achieves **CV R² ≈ 0.63** with **RMSE ≈ 0.78**, adequate for group-level insights.
