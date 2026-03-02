# Data Analysis

This directory contains the processed quantitative proteomics data and associated sample metadata used for downstream analysis.

---

## Directory Structure

```
data_analysis/
├── data/
│   ├── precursors_normalized_wide.tsv
│   └── proteins_normalized_wide.tsv
└── metadata/
    └── metadata_wide.tsv
```

---

## data/

### `precursors_normalized_wide.tsv`

Peptide-level abundance data in wide format. Each row represents a unique precursor ion and each column (after the first three) represents a sample.

| Column | Description |
|--------|-------------|
| `protein` | Protein identifier associated with the peptide |
| `modifiedSequence` | Peptide sequence including any modifications (e.g., `LSC[+57]TASGFNIK`) |
| `precursorCharge` | Precursor ion charge state |
| *(remaining columns)* | Per-sample abundance values; column names correspond to the `replicate` column in `metadata_wide.tsv` |

### `proteins_normalized_wide.tsv`

Protein-level abundance data in wide format. Each row represents a unique protein and each column (after the first) represents a sample.

| Column | Description |
|--------|-------------|
| `protein` | Protein identifier |
| *(remaining columns)* | Per-sample abundance values; column names correspond to the `replicate` column in `metadata_wide.tsv` |

> **Note:** All abundance values in both files have been median normalized and are reported as log2-transformed values.

---

## metadata/

### `metadata_wide.tsv`

Sample metadata in wide format. Each row corresponds to a single sample run. The `replicate` column matches the sample column headers in both abundance data files and can be used to join metadata to quantitative data.

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
