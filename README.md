# Intracranial EEG Seizure Analysis: HUP164

This repository contains an exploratory research workflow for analyzing intracranial EEG/iEEG seizure recordings from the HUP iEEG epilepsy dataset. The project focuses first on one patient, **sub-HUP164**, and compares three ictal seizure recordings.

The main aim is to understand how seizure onset zone channels behave before, during, and after seizure onset, and to compare them with non-SOZ channels using visual inspection and numerical feature extraction.

## Project status

This is an exploratory research project. It is not a clinical diagnostic tool. The goal is to learn the dataset structure, understand iEEG signals, extract useful features, and prepare a foundation for later machine learning or graph-based seizure analysis.
    
## Dataset

The raw dataset is not included in this repository because the EDF files are large and should be downloaded from the official dataset source by the user.

Expected local folder structure:

```text
HUP_iEEG_Epilepsy_Dataset/
└── sub-HUP164/
    └── ses-presurgery/
        └── ieeg/
            ├── sub-HUP164_ses-presurgery_task-ictal_acq-seeg_run-01_ieeg.edf
            ├── sub-HUP164_ses-presurgery_task-ictal_acq-seeg_run-01_channels.tsv
            ├── sub-HUP164_ses-presurgery_task-ictal_acq-seeg_run-01_events.tsv
            ├── sub-HUP164_ses-presurgery_task-ictal_acq-seeg_run-01_ieeg.json
            ├── sub-HUP164_ses-presurgery_task-ictal_acq-seeg_run-02_...
            └── sub-HUP164_ses-presurgery_task-ictal_acq-seeg_run-03_...
```

You will need to edit `DATA_DIR` inside the notebooks so it points to your local dataset folder.

## Notebooks

| Notebook | Purpose |
|---|---|
| `notebooks/01_understand_HUP164_RUN1.ipynb` | Understand one ictal recording, inspect metadata, plot SOZ channels, and extract features for run-01. |
| `notebooks/02_compare_HUP164_ictal_runs.ipynb` | Repeat the same analysis for ictal run-01, run-02, and run-03, then compare their feature patterns. |

## Results included

The `results/` folder contains CSV summaries extracted from the analysis notebooks.

| File | Description |
|---|---|
| `run_summary_HUP164_ictal_runs.csv` | Basic information for run-01, run-02, and run-03. |
| `events_summary_HUP164_ictal_runs.csv` | Seizure onset and offset events for all three runs. |
| `analysis_regions_HUP164_ictal_runs.csv` | Time regions used for feature extraction. |
| `feature_summary_SOZ_vs_nonSOZ_HUP164.csv` | Mean feature values grouped by run, region, and SOZ/non-SOZ label. |
| `preictal_to_early_ictal_fold_change_HUP164.csv` | Fold-change from preictal candidate region to early ictal region. |
| `things_learned_summary.csv` | Short summary of the main concepts learned during the project. |

## Important terms learned

### iEEG
Intracranial EEG. This is brain electrical activity recorded from electrodes placed inside the skull or directly in/on the brain.

### SEEG
Stereo-electroencephalography. This uses depth electrodes placed inside the brain to record signals from deeper brain regions.

### Channel
A channel is one recorded signal from one electrode/contact. In this project, each channel is treated as one brain signal over time.

### Ictal
The seizure period.

### Interictal
The time between seizures.

### Preictal
The time before seizure onset. In this project, the last 60 seconds before seizure onset were treated as a **preictal candidate** region.

### SOZ
Seizure onset zone. These are clinically marked channels where seizures are believed to start.

### Bad channel
A noisy or non-useful channel that should be removed before analysis.

### events.tsv
This file tells when seizure events happen. For example, it gives seizure onset and seizure offset in seconds and sample numbers.

### channels.tsv
This file tells information about each channel, including channel name, type, sampling frequency, good/bad status, SOZ label, and resection label.

### ieeg.json
This metadata file tells how the iEEG recording was collected, including sampling frequency, recording duration, recording type, and power-line frequency.

## Analysis workflow

The workflow followed these steps:

1. Loaded patient HUP164 data.
2. Identified all ictal and interictal EDF files.
3. Selected ictal run-01 for detailed study.
4. Read `channels.tsv`, `events.tsv`, and `ieeg.json` files.
5. Found seizure onset and seizure offset times.
6. Identified bad channels and SOZ channels.
7. Removed bad channels from the signal.
8. Visualized iEEG signals around seizure onset.
9. Visualized SOZ channels only.
10. Compared channel LA1 before and during seizure.
11. Created analysis regions such as early-before, preictal candidate, early ictal, middle ictal, late ictal, and postictal.
12. Extracted numerical features for every channel and every region.
13. Repeated the same analysis for run-01, run-02, and run-03.
14. Compared SOZ and non-SOZ channels across all three runs.

## Features extracted

The project extracted these features from each channel:

| Feature | Meaning |
|---|---|
| `rms_uv` | Average signal strength/amplitude. |
| `peak_to_peak_uv` | Difference between maximum and minimum signal value. |
| `std_uv` | Signal variability. |
| `line_length_uv` | How rapidly the signal changes; useful for seizure-like activity. |
| `delta_power` | Power in 1-4 Hz slow activity. |
| `theta_power` | Power in 4-8 Hz activity. |
| `alpha_power` | Power in 8-13 Hz activity. |
| `beta_power` | Power in 13-30 Hz activity. |
| `gamma_power` | Power in 30-80 Hz activity. |
| `high_gamma_power` | Power in 80-150 Hz activity. |

## Key findings from HUP164

For the three ictal runs, the seizure onset time was 120 seconds in all runs. The seizure offsets were different: about 186 seconds for run-01, 185 seconds for run-02, and 203 seconds for run-03.

Across all three runs, the clinically marked SOZ channels were consistent:

```text
LA1, LA2, LA3, LB1, LB2, LB3, LC1, LC2, LC3
```

The bad channels were also consistent:

```text
EKG1, EKG2, LD11, LD12
```

The most important early result is that SOZ channels show increased gamma and high-gamma power after seizure onset, especially in run-01 and run-02. This suggests that high-frequency iEEG features may be useful for identifying seizure-related activity.

The fold-change analysis showed:

| Run | Group | Gamma fold-change | High-gamma fold-change |
|---|---:|---:|---:|
| run-01 | SOZ | 2.49 | 2.53 |
| run-02 | SOZ | 2.02 | 1.78 |
| run-03 | SOZ | 1.16 | 1.33 |

This means SOZ gamma/high-gamma activity increased most strongly in run-01 and run-02 immediately after seizure onset.

## What I learned from this project

- How brain data is organized in BIDS format.
- How to load iEEG EDF files using MNE-Python.
- How to read event, channel, and metadata files.
- How to identify seizure onset and offset.
- How to remove bad channels before analysis.
- How to understand SOZ and resection labels.
- How to plot raw iEEG signals around seizure onset.
- How to extract signal features from iEEG data.
- How to compare SOZ and non-SOZ channels.
- How to compare multiple seizures from the same patient.
- Why gamma and high-gamma activity can be important in seizure analysis.

## How to run

Install the required packages:

```bash
pip install -r requirements.txt
```

Open JupyterLab:

```bash
jupyter lab
```

Then run the notebooks in order:

```text
01_understand_HUP164_RUN1.ipynb
02_compare_HUP164_ictal_runs.ipynb
```

Make sure to update the data path inside the notebooks before running:

```python
DATA_DIR = Path(r"your_local_path_to_HUP_iEEG_Epilepsy_Dataset")
```

## Future work

Possible next steps:

1. Add interictal runs and compare seizure vs non-seizure activity.
2. Build a machine learning classifier for SOZ vs non-SOZ channels.
3. Use sliding-window analysis to detect changes before seizure onset.
4. Build brain connectivity graphs using correlation or coherence.
5. Train a graph neural network or transformer model for explainable seizure prediction.
6. Compare model-important channels with clinically marked SOZ and resection channels.

## Notes for GitHub

Do not upload raw EDF files to GitHub. They are large and should stay outside the repository. Keep only notebooks, code, small CSV result files, and documentation.
