# SuperCam LIBS Acoustic Analysis

This repository contains the cleaned processing and analysis workflow, datasets, raw acoustic products, quality-control records, figures, and supporting documentation for a study of SuperCam Laser-Induced Breakdown Spectroscopy (LIBS) acoustic signals recorded by the Perseverance rover in Jezero crater.

The project began with Crater Floor observations and was later expanded to the Delta Front and Upper Fan. The final combined dataset contains 293 observations:

- 185 Crater Floor
- 53 Delta Front
- 55 Upper Fan

The final regional analysis compares acoustic behavior across these three regions using time-domain reverberation metrics, FFT-derived frequency metrics, nonparametric statistical tests, and principal component analysis (PCA).

## Repository layout

```text
repo/
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   ├── 01_crater_floor_processing.ipynb
│   ├── 02_crater_floor_qc_recovery.ipynb
│   ├── 03_delta_upper_fan_processing.ipynb
│   └── 04_statistical_analysis.ipynb
│
├── data/
│   ├── raw/
│   │   ├── crater_floor_wav/
│   │   └── delta_upper_fan_zips/
│   │       ├── Delta/
│   │       └── Upper Fan/
│   ├── intermediate/
│   ├── processed/
│   └── qc/
│
├── figures/
├── results/
├── docs/
│
└── archive/
    └── original_notebooks/
```

## Recommended starting point

For a quick overview of the project, read the handoff report in `docs/`.

For reproducing the final statistical results and figures, the main files are:

```text
data/processed/LIBS_acoustic_meta_sheet_v4_293.csv
notebooks/04_statistical_analysis.ipynb
```

Notebook 04 can be run without repeating the earlier raw-acoustic processing stages.

## Main datasets

### `data/processed/LIBS_acoustic_meta_sheet_v3_185.csv`

Final Crater Floor dataset after onset detection, manual review, and two rounds of recovery. It contains 185 observations.

### `data/processed/LIBS_acoustic_meta_sheet_v4_293.csv`

Final combined dataset used for the regional statistical analysis and PCA. It contains:

```text
185 Crater Floor
 53 Delta Front
 55 Upper Fan
---
293 total
```

## Raw acoustic data

Raw acoustic products used in the project are retained in `data/raw/`.

### Crater Floor

`data/raw/crater_floor_wav/`

This directory contains the retained Crater Floor acoustic products, including the WAV files used by notebook 01 and retained FITS products for provenance.

The early Crater Floor workflow converted SuperCam FITS products to WAV before metric calculation. The original FITS-to-WAV conversion code from that early stage was not retained, so the WAV files are the reproducible inputs for the cleaned Crater Floor processing notebook.

### Delta Front and Upper Fan

```text
data/raw/delta_upper_fan_zips/
├── Delta/
└── Upper Fan/
```

These directories contain the original downloaded SuperCam ZIP archives used for the later regional expansion.

The Delta Front and Upper Fan products are intentionally retained as ZIP archives rather than as separately extracted FITS files. This reflects the workflow used during the project, where keeping the downloaded products in ZIP form provided a workaround for an extraction/access issue encountered with some products. Notebook 03 reads the relevant FITS product directly from each archive, extracts the `SOUND` shot arrays, writes a concatenated multishot WAV intermediate, and analyzes the second shot.

The concatenated WAV files generated during this process belong in:

```text
data/intermediate/concatenated_multishot_wav/
```

and do not need to be treated as separate raw inputs.

## Analysis workflow

The cleaned notebooks are ordered according to the project workflow.

### 01 — Crater Floor processing

`notebooks/01_crater_floor_processing.ipynb`

Processes the retained Crater Floor WAV files and calculates the time- and frequency-domain acoustic metrics used in the project.

Input:

```text
data/raw/crater_floor_wav/
```

Output:

```text
data/intermediate/LIBS_acoustic_frequency_results.csv
```

Historical result:

```text
209 processed Crater Floor observations
```

### 02 — Crater Floor QC and recovery

`notebooks/02_crater_floor_qc_recovery.ipynb`

Preserves the manual onset-recovery stages used to expand the usable Crater Floor dataset.

Historical progression:

```text
127 initially usable observations
+43 first-pass recoveries
=170 observations

+15 second-pass recoveries
=185 final Crater Floor observations
```

Important retained files include:

```text
data/intermediate/LIBS_acoustic_meta_sheet_expanded.csv
data/processed/LIBS_acoustic_meta_sheet_v3_185.csv
data/qc/onset_recovery_summary.csv
data/qc/recovery_pass_2_summary.csv
```

The original 127-row geological metadata sheet was not retained. The retained 170-row expanded dataset therefore serves as the authoritative intermediate dataset for the second recovery stage.

### 03 — Delta Front and Upper Fan processing

`notebooks/03_delta_upper_fan_processing.ipynb`

Processes the Delta Front and Upper Fan acoustic products and combines accepted observations with the final Crater Floor dataset.

Workflow:

1. Read the selected SuperCam FITS product from each downloaded archive.
2. Extract the `SOUND` `Shot0...ShotN` arrays.
3. Concatenate the shot arrays into a multishot WAV.
4. Analyze the second shot at the first raster position.
5. Detect acoustic onset and extract the 10 ms post-onset analysis window.
6. Calculate the same acoustic metrics used for the Crater Floor.
7. Retain accepted observations and preserve flagged observations for QC.
8. Append accepted observations to the 185-row Crater Floor dataset.

Historical progression:

```text
225 FITS products attempted
221 successfully converted / processed

108 accepted
 53 Delta Front
 55 Upper Fan

185 Crater Floor
+108 accepted Delta Front / Upper Fan
=293 final observations
```

QC outputs are retained in:

```text
data/qc/
```

The final combined dataset is written to:

```text
data/processed/LIBS_acoustic_meta_sheet_v4_293.csv
```

### 04 — Statistical analysis and figures

`notebooks/04_statistical_analysis.ipynb`

Runs the final analysis on the 293-observation dataset.

It includes:

- regional summary statistics
- Kruskal-Wallis tests
- epsilon-squared effect sizes
- Holm-corrected Dunn post hoc tests
- Maaz vs. Seitah comparisons within the Crater Floor
- Crater Floor member-level comparisons
- Spearman correlations between acoustic metrics
- PCA and PCA loadings
- final statistical tables and figures

Notebook 04 was rerun during the handoff process and reproduced the historical final statistical results and figures.

## Acoustic metrics

All primary acoustic metrics were calculated from a fixed 10 ms signal window beginning at the detected acoustic onset.

### Time-domain metrics

- EDC slope / reported decay rate
- C2

The Energy Decay Curve slope was fit over approximately -7 to -15 dB, targeting a relatively stable, approximately linear portion of the decay while avoiding the immediate post-shot transient and lower-amplitude tail.

The stored `Slope (dB/s)` values are negative. Figures and report text may display positive decay rate as `-Slope`, so larger values correspond to faster acoustic decay.

### Frequency-domain metrics

- spectral centroid
- spectral bandwidth
- peak frequency
- 85% spectral rolloff
- low-frequency power, 1-10 kHz
- high-frequency power, 10-30 kHz
- high/low power ratio
- high-frequency fraction
- total FFT power

Eight selected acoustic metrics were used in the final regional Kruskal-Wallis analysis and PCA.

## Quality-control and intermediate files

### `data/intermediate/`

Contains reproducible intermediate datasets and generated intermediate acoustic products, including:

- `LIBS_acoustic_frequency_results.csv`
- `LIBS_acoustic_meta_sheet_expanded.csv`
- generated concatenated multishot WAV files, if notebook 03 is rerun

### `data/qc/`

Contains records of manual onset review, recovery, acceptance, flagging, and processing history, including:

- `Acoustic Wave Data - WIP.csv`
- `Acoustic Wave Data - Recover.csv`
- `Acoustic Wave Data - Recover Pass2.csv`
- `onset_recovery_summary.csv`
- `recovery_pass_2_summary.csv`
- `concatenation_log.csv`
- `LIBS_acoustic_all_results.csv`
- `LIBS_acoustic_accepted_results.csv`
- `LIBS_acoustic_flagged_results.csv`

These files are retained because some manual review decisions cannot be reconstructed from the final combined CSV alone.

## Figures

Final report figures are stored in `figures/`, including:

- `Regional_Acoustic_Metrics.png`
- `PCA_SuperCam_LIBS_Acoustic_Metrics.png`
- `Kruskal_Wallis_Table.png`

Additional QC figures may be generated by the processing notebooks.

## Results

`results/` is used for statistical tables generated by notebook 04. These outputs can be regenerated from the 293-row processed dataset.

## Supporting documentation

`docs/` contains the project handoff material, including the final handoff report and other supporting project documents retained for continuity.

The handoff report summarizes the project motivation, dataset, processing methodology, acoustic metrics, statistical results, and repository organization. The README is intended to provide the more detailed file and workflow instructions.

## Original notebooks

The original working notebooks are preserved unchanged in:

```text
archive/original_notebooks/
```

They contain exploratory code, superseded methods, plotting experiments, and the exact historical working environment of the project.

The four cleaned notebooks in `notebooks/` are the preferred entry point for future use. The archived originals are retained for provenance and troubleshooting.

## Quick start: reproduce the final analysis

From the repository root:

1. Install the Python dependencies:

   ```bash
   pip install -r requirements.txt
   ```

2. Confirm that the final dataset exists at:

   ```text
   data/processed/LIBS_acoustic_meta_sheet_v4_293.csv
   ```

3. Open:

   ```text
   notebooks/04_statistical_analysis.ipynb
   ```

4. Run all cells.

Generated statistical outputs are written to `results/`, and final figures are written to `figures/`.

## Data provenance

The SuperCam acoustic products used in this project were obtained from the publicly available Mars 2020 Perseverance Analyst's Notebook maintained by NASA's Planetary Data System Geosciences Node at Washington University in St. Louis.

All source observations originated from SuperCam FITS products.

The Crater Floor portion of the project was originally processed from WAV files converted from those FITS products. The Delta Front and Upper Fan workflow retained in notebook 03 extracts `SOUND` arrays from the source FITS products and creates concatenated multishot WAV intermediates before metric calculation.

## Project status at handoff

At handoff, the project contains a complete 293-observation regional dataset spanning the Crater Floor, Delta Front, and Upper Fan, together with the cleaned processing workflow and final regional statistical analysis.

Additional Delta Front and Upper Fan observations remain in the QC records if future work revisits onset recovery or expands the final accepted dataset.
