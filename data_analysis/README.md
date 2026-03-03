# Data Analysis

This directory contains the processed quantitative proteomics data and associated sample metadata used for downstream analysis.

---

## Directory Structure

```
data_analysis/
├── peptide-fixed-effect-ols.ipynb
├── protein-fixed-effect-ols.ipynb
├── protein-fixed-effect-ols-sex-age-ixn.ipynb
├── find-significant-peptides-not-significant-proteins.ipynb
├── peptide-regression-model.ipynb
├── protein-regression-model.ipynb
├── protein-cv-linear-model.ipynb
├── data/
│   ├── precursors_normalized_wide.tsv
│   └── proteins_normalized_wide.tsv
├── metadata/
│   └── metadata_wide.tsv
└── results/
    ├── peptides-mouse-aging-features-ols.csv
    ├── proteins-mouse-aging-features-ols.csv
    ├── mouse-sex-specific-age-effect-features-ols.csv
    ├── peptide_protein_q_table.csv
    ├── peptide-age-regression-feature-importances.csv
    ├── peptide-age-regression-final-model-coefficients.csv
    ├── peptide-age-regression-scatter-boxplot.{pdf,svg,png}
    ├── peptide-age-regression-confusion-matrix.{pdf,svg,png}
    ├── protein-age-regression-feature-importances.csv
    ├── protein-age-regression-final-model-coefficients.csv
    ├── protein-age-regression-scatter-boxplot.{pdf,svg,png}
    └── protein-age-regression-confusion-matrix.{pdf,svg,png}
```

---

## Notebooks

### `peptide-fixed-effect-ols.ipynb`

Fits a fixed-effects ordinary least squares (OLS) regression model to peptide-level abundance data to identify individual peptides (precursor ions) whose abundance changes with age, controlling for sex as a covariate.

**Inputs:**
- `data/precursors_normalized_wide.tsv` — peptide-level abundance data (log2, median normalized)
- `metadata/metadata_wide.tsv` — sample metadata; QC samples (those lacking an `Age_months` value) are excluded from the analysis

**Pre-processing:** Duplicate peptide entries (same modified sequence appearing under multiple proteins) are deduplicated by modified sequence. Where a peptide maps to multiple proteins, the protein identifiers are merged into a comma-delimited string. Each row in the results is labeled as `modifiedSequence (protein)` for traceability.

**Model:**

For each peptide precursor, the following additive OLS model is fit:

```
peptide ~ month + sex
        = β0 + β1·month + β2·sex[Female]
```

where `month` is the animal's age in months and `sex` is included as a categorical covariate (Male as reference). Abundances are un-logged (2^x), re-transformed with log1p, and standard-scaled prior to fitting. P-values for the age slope (`β1`) are corrected for multiple testing using the Benjamini–Hochberg (BH) FDR procedure.

**Output:** `results/peptides-mouse-aging-features-ols.csv`

One row per unique peptide with the following columns:

| Column | Description |
|--------|-------------|
| `protein` | Label formatted as `modifiedSequence (protein_name)`; if the peptide maps to multiple proteins, protein names are comma-delimited |
| `coef_month` | OLS slope for age (months), controlling for sex |
| `std_err` | Standard error of `coef_month` |
| `p_value` | Two-sided p-value for the age slope |
| `q_value` | BH-adjusted FDR for the age slope |
| `ci_lower` / `ci_upper` | Confidence interval bounds for `coef_month` |
| `minus_log10_p` | −log10(p_value), for convenience |
| `signif_FDR<0.010` | `True` if `q_value` < 0.01 |
| `error` | Error message if model fitting failed for this peptide (otherwise absent) |

---

### `protein-fixed-effect-ols.ipynb`

Fits a fixed-effects ordinary least squares (OLS) regression model to protein-level abundance data to identify proteins whose abundance changes with age, controlling for sex as a covariate.

**Inputs:**
- `data/proteins_normalized_wide.tsv` — protein-level abundance data (log2, median normalized)
- `metadata/metadata_wide.tsv` — sample metadata; QC samples (those lacking an `Age_months` value) are excluded from the analysis

**Model:**

For each protein, the following additive OLS model is fit:

```
protein ~ month + sex
        = β0 + β1·month + β2·sex[Female]
```

where `month` is the animal's age in months and `sex` is included as a categorical covariate (Male as reference) but no interaction between age and sex is modeled. Abundances are un-logged (2^x), re-transformed with log1p, and standard-scaled prior to fitting. P-values for the age slope (`β1`) are corrected for multiple testing using the Benjamini–Hochberg (BH) FDR procedure.

**Output:** `results/proteins-mouse-aging-features-ols.csv`

One row per protein with the following columns:

| Column | Description |
|--------|-------------|
| `protein` | Protein identifier |
| `coef_month` | OLS slope for age (months), controlling for sex |
| `std_err` | Standard error of `coef_month` |
| `p_value` | Two-sided p-value for the age slope |
| `q_value` | BH-adjusted FDR for the age slope |
| `ci_lower` / `ci_upper` | Confidence interval bounds for `coef_month` |
| `minus_log10_p` | −log10(p_value), for convenience |
| `signif_FDR<0.010` | `True` if `q_value` < 0.01 |
| `error` | Error message if model fitting failed for this protein (otherwise absent) |

---

### `protein-fixed-effect-ols-sex-age-ixn.ipynb`

Fits a fixed-effects ordinary least squares (OLS) regression model to protein-level abundance data to identify proteins whose abundance changes with age and to test whether those age-related changes differ between sexes.

**Inputs:**
- `data/proteins_normalized_wide.tsv` — protein-level abundance data (log2, median normalized)
- `metadata/metadata_wide.tsv` — sample metadata; QC samples (those lacking an `Age_months` value) are excluded from the analysis

**Model:**

For each protein, the following OLS model is fit:

```
protein ~ month * sex
        = β0 + β1·month + β2·sex[Female] + β3·(month × sex[Female])
```

where `month` is the animal's age in months and `sex` is treatment-coded with Male as the reference. Abundances are un-logged (2^x), re-transformed with log1p, and standard-scaled prior to fitting. P-values for the age slope (`β1`) and the sex × age interaction term (`β3`) are corrected for multiple testing using the Benjamini–Hochberg (BH) FDR procedure.

**Output:** `results/mouse-sex-specific-age-effect-features-ols.csv`

One row per protein with the following columns:

| Column | Description |
|--------|-------------|
| `protein` | Protein identifier |
| `beta_month` | OLS slope for age (months) in males (reference sex) |
| `se_month` | Standard error of `beta_month` |
| `p_month` | Two-sided p-value for the age slope |
| `q_month` | BH-adjusted FDR for the age slope |
| `ci_month_low` / `ci_month_high` | Confidence interval bounds for `beta_month` |
| `beta_int` | Interaction coefficient (difference in age slope: female − male) |
| `se_int` | Standard error of `beta_int` |
| `p_int` | Two-sided p-value for the sex × age interaction |
| `q_int` | BH-adjusted FDR for the interaction term |
| `ci_int_low` / `ci_int_high` | Confidence interval bounds for `beta_int` |
| `signif_month` | `True` if `q_month` < 0.01 |
| `signif_interaction` | `True` if `q_int` < 0.01 |
| `error` | Error message if model fitting failed for this protein (otherwise absent) |

---

### `find-significant-peptides-not-significant-proteins.ipynb`

Cross-references the peptide-level and protein-level OLS results to identify peptides that are statistically significant with respect to age but whose parent protein is not. Also produces a scatter plot comparing peptide and protein −log10(q) values and fits a linear regression to characterize the overall concordance between the two levels of analysis.

**Inputs:**
- `results/peptides-mouse-aging-features-ols.csv` — output of `peptide-fixed-effect-ols.ipynb`
- `results/proteins-mouse-aging-features-ols.csv` — output of `protein-fixed-effect-ols.ipynb`

**Output:** `results/peptide_protein_q_table.csv`

One row per (peptide, protein) pair with the following columns:

| Column | Description |
|--------|-------------|
| `peptide` | Modified peptide sequence |
| `peptide_q_value` | BH-adjusted FDR for the age effect at the peptide level |
| `protein` | Protein identifier associated with the peptide |
| `protein_q_value` | BH-adjusted FDR for the age effect at the protein level; `NaN` if the protein was not found in the protein-level results |

The notebook also produces a scatter plot of −log10(protein q-value) vs −log10(peptide q-value) for all peptide–protein pairs, with a linear regression line and R² reported. Peptides that are significant (q < 0.01) while their parent protein is not are highlighted in red.

---

### `peptide-regression-model.ipynb`

Trains an ElasticNet regularized regression model to predict animal age (in months) from peptide-level abundances. Model performance is evaluated using repeated k-fold cross-validation, and feature importances (average ElasticNet coefficients across folds) are saved for downstream interpretation. A final model is then trained on all available data using the same hyperparameters, and the resulting per-peptide coefficients are saved to a separate report.

**Inputs:**
- `data/precursors_normalized_wide.tsv` — peptide-level abundance data (log2, median normalized)
- `metadata/metadata_wide.tsv` — sample metadata; QC samples (those lacking an `Age_months` value) are excluded

**Pre-processing:** Duplicate peptide entries are deduplicated by modified sequence. Abundances are un-logged (2^x), then re-transformed with log10. Sex is encoded as a binary feature (Male = 0, Female = 1) and appended as an additional predictor column.

**Model:** ElasticNet regression (`alpha=0.01`, `l1_ratio=0.3`) fit to predict age in months. Model performance is assessed with repeated k-fold cross-validation (10 splits × 5 repeats). Features are standard-scaled within each training fold. Performance metrics reported include cross-validation MAE, naive MAE baseline (mean-prediction), and R² of predicted vs. true age.

**Outputs:**
- `results/peptide-age-regression-feature-importances.csv` — per-feature average ElasticNet coefficients across all CV folds
- `results/peptide-age-regression-final-model-coefficients.csv` — per-feature coefficients from a final ElasticNet model trained on all data
- `results/peptide-age-regression-scatter-boxplot` — box plot with individual prediction points overlaid, saved as `.pdf`, `.svg`, and `.png`
- `results/peptide-age-regression-confusion-matrix` — confusion matrix of discretized predicted vs. true ages, saved as `.pdf`, `.svg`, and `.png`

`peptide-age-regression-feature-importances.csv` columns:

| Column | Description |
|--------|-------------|
| `feature` | Peptide label (`modifiedSequence (protein)`) or `sex` for the sex covariate |
| `coefficient` | Average ElasticNet coefficient across all CV folds; zero for features excluded by regularization |
| `nonzero_count` | Number of CV folds (out of `splits × repeats` total) in which the feature received a non-zero coefficient |

`peptide-age-regression-final-model-coefficients.csv` columns:

| Column | Description |
|--------|-------------|
| `feature` | Peptide label (`modifiedSequence (protein)`) or `sex` for the sex covariate |
| `coefficient` | ElasticNet coefficient from the final model trained on all data; zero for features excluded by regularization |

---

### `protein-regression-model.ipynb`

Trains an ElasticNet regularized regression model to predict animal age (in months) from protein-level abundances. This is the protein-level counterpart to `peptide-regression-model.ipynb`, using the same cross-validation scheme and model configuration. A final model is then trained on all available data using the same hyperparameters, and the resulting per-protein coefficients are saved to a separate report.

**Inputs:**
- `data/proteins_normalized_wide.tsv` — protein-level abundance data (log2, median normalized)
- `metadata/metadata_wide.tsv` — sample metadata; QC samples (those lacking an `Age_months` value) are excluded

**Pre-processing:** Abundances are un-logged (2^x), then re-transformed with log10. Sex is encoded as a binary feature (Male = 0, Female = 1) and appended as an additional predictor column.

**Model:** ElasticNet regression with repeated k-fold cross-validation (10 splits × 5 repeats). Features are standard-scaled within each training fold. Performance metrics reported include cross-validation MAE, naive MAE baseline, and R² of predicted vs. true age.

**Outputs:**
- `results/protein-age-regression-feature-importances.csv` — per-feature average ElasticNet coefficients across all CV folds
- `results/protein-age-regression-final-model-coefficients.csv` — per-feature coefficients from a final ElasticNet model trained on all data
- `results/protein-age-regression-scatter-boxplot` — box plot with individual prediction points overlaid, saved as `.pdf`, `.svg`, and `.png`
- `results/protein-age-regression-confusion-matrix` — confusion matrix of discretized predicted vs. true ages, saved as `.pdf`, `.svg`, and `.png`

`protein-age-regression-feature-importances.csv` columns:

| Column | Description |
|--------|-------------|
| `feature` | Protein identifier or `sex` for the sex covariate |
| `coefficient` | Average ElasticNet coefficient across all CV folds; zero for features excluded by regularization |
| `nonzero_count` | Number of CV folds (out of `splits × repeats` total) in which the feature received a non-zero coefficient |

`protein-age-regression-final-model-coefficients.csv` columns:

| Column | Description |
|--------|-------------|
| `feature` | Protein identifier or `sex` for the sex covariate |
| `coefficient` | ElasticNet coefficient from the final model trained on all data; zero for features excluded by regularization |

---

### `protein-cv-linear-model.ipynb`

Models the coefficient of variation (CV) of protein abundance as a function of mean abundance, age group, and sex using OLS linear regression. This is used to assess whether inter-sample variability in protein abundance is systematically associated with age or sex.

**Inputs:**
- `data/proteins_normalized_wide.tsv` — protein-level abundance data (log2, median normalized)
- `metadata/metadata_wide.tsv` — sample metadata; QC samples (those lacking an `Age_months` value) are excluded

**Approach:** Abundances are un-logged (2^x) and re-transformed with log1p. For each protein, the CV (std / mean) of the transformed abundance is computed within each (age group, sex) stratum. Two separate OLS models are then fit:

1. **All-age model** — age groups split at 21 months (`isOld`: 0 = ≤21 months, 1 = >21 months)
2. **Restricted-age model** — only samples aged 5–15 months (young) or 17–21 months (older) are included; samples >21 months are excluded (`isOld`: 0 = 5–15 months, 1 = 17–21 months)

Both models take the form:

```
CV ~ meanAbundance + isOld + isMale
```

where one row per (protein, age group, sex) stratum is used as the unit of observation.

**Outputs:** OLS model summaries printed to the notebook. No files are written to disk.

---

## data/

### `precursors_normalized_wide.tsv`

Peptide-level abundance data. Each row represents a unique precursor ion and each column (after the first three) represents a sample.

| Column | Description |
|--------|-------------|
| `protein` | Protein identifier associated with the peptide |
| `modifiedSequence` | Peptide sequence including any modifications (e.g., `LSC[+57]TASGFNIK`) |
| `precursorCharge` | Precursor ion charge state |
| *(remaining columns)* | Per-sample abundance values; column names correspond to the `replicate` column in `metadata_wide.tsv` |

### `proteins_normalized_wide.tsv`

Protein-level abundance data. Each row represents a unique protein and each column (after the first) represents a sample.

| Column | Description |
|--------|-------------|
| `protein` | Protein identifier |
| *(remaining columns)* | Per-sample abundance values; column names correspond to the `replicate` column in `metadata_wide.tsv` |

> **Note:** All abundance values in both files have been median normalized and are reported as log2-transformed values.

---

## metadata/

### `metadata_wide.tsv`

Sample metadata. Each row corresponds to a single sample run. The `replicate` column matches the sample column headers in both abundance data files and can be used to join metadata to quantitative data.

Samples include both biological specimens and two types of pooled quality control (QC) samples:
- **IEQC** (In-experiment QC): pooled plasma QC injected throughout the run to monitor instrument stability
- **IBQC** (Inter-batch QC): pooled plasma QC used to assess variation across batches

Columns:

| Column | Description |
|--------|-------------|
| `replicate` | Unique sample run identifier; matches column headers in the data files |
| `project` | Project name (all samples: `Aging_Mouse_EV`) |
| `ticArea` | Total ion chromatogram (TIC) area; instrument-level signal intensity metric |
| `acquiredRank` | Rank of this sample in the order samples were acquired by the mass spectrometer |
| `MS.Queue.order` | Position of this sample in the MS acquisition queue |
| `Plate.Order` | Combined plate number and well position (e.g., `1_A01` = plate 1, row A, well 1) |
| `MacCoss.ID` | Internal sample identifier (e.g., `JXC0001`, `IEQC01`, `IBQC02`) |
| `Condition..Original.group.number.` | Original condition or group number assignment; numeric for biological samples, `IEQC` or `IBQC` for QC samples |
| `Sample.Name` | Human-readable sample name |
| `Animal.ID` | Unique animal identifier (e.g., `AgedB6-2635`); `JXC-IE-PL` or `JXC-IB-PL` for pooled QC |
| `SampleGroup` | Sample group label; numeric groups (1–9) correspond to biological age cohorts; `IEQC` and `IBQC` are QC pools |
| `Sex` | Biological sex of the animal (`Male`, `Female`); `Mixed` for pooled QC samples |
| `Age_months` | Age of the animal at time of sample collection, in months; `NA` for QC samples |
| `Age_weeks` | Age of the animal at time of sample collection, in weeks; `NA` for QC samples |
| `Marker` | Ear punch pattern used for physical animal identification (e.g., `2R`, `3L`, `B`, `N`); `NA` for QC samples |
| `Housing.ID` | Cage/housing identifier for the animal; `NA` for QC samples |
| `Cohort` | Study cohort description (e.g., `Aged_B6 24-Month Cohort`); `NA` for QC samples |
| `Hemolysis` | Degree of hemolysis observed in the sample (ordinal: `Very low`, `Low`, `Moderately low`, `Moderate`, `Moderately severe`, `Severe`, `Very severe`) |
| `Plate` | Sample preparation plate number (`1` or `2`) |
| `Row` | Row position on the sample preparation plate (`A`–`E`) |
| `Position` | Well position on the sample preparation plate |
| `Run.Order` | Final MS acquisition run order |
| `Group.Order` | Acquisition order within each sample group |
| `TechnicalReplicate` | Technical replicate number |
| `Comments` | Free-text comments field |
