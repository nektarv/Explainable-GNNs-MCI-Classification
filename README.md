# Explainable GNNs for MCI Detection from Structural Brain Connectivity

This repository contains the source code developed for the semester project of the **m-health and e-health Technologies** course at the **School of Electrical and Computer Engineering, NTUA**.

## Overview

This project explores the use of **Explainable Artificial Intelligence (XAI)** and **Graph Neural Networks (GNNs)** for the analysis of **brain connectivity networks derived from sMRI and dMRI data**. The goal is to investigate how interpretable machine learning methods can support the analysis and understanding of **Mild Cognitive Impairment (MCI)** through structural brain connectivity.

The classification task is binary: **Cognitively Normal (CN) vs MCI**, using data from the **OASIS-3** dataset.

---

## Repository structure

```
.
├── preprocessing/       # Preprocessing pipeline (sMRI + DTI → graphs)
│   └── README.md
├── models/              # GNN model training, nested CV, filtering & ensemble
│   └── README.md
├── xai/                 # XAI methods applied to the best-performing model
│   └── README.md
├── oasis3_t1.csv        # T1 volumetric features (sMRI), included in this repo
└── README.md
```

Each subfolder contains its own README with a detailed description of the methodology.

---

## Data

This project uses two data modalities from the **OASIS-3** dataset:

- **sMRI (T1-weighted)** - volumetric features extracted per brain region, provided as `oasis3_t1.csv` in this repository.
- **dMRI (DTI)** - structural connectivity graphs encoding white matter pathways between brain regions. The graph files can be downloaded from the following link:
  [https://braingraph.org/static/oasis3_graphmls_scale1.7z](https://braingraph.org/static/oasis3_graphmls_scale1.7z)

---

## Workflow

**Important:** The notebooks in this repository are organised into subfolders for clarity, but were originally developed in a flat directory structure. To run them, **place all notebooks and data files in a folder named team3_xai_gnn** and run everything from that base directory. Running them from within their respective subfolders will cause path errors.

The intended execution order is:

1. **Preprocessing** - Run the preprocessing notebook to generate the fold-wise graph datasets from the raw T1 and DTI data. Note that the preprocessed outputs are too large to include in this repository and must be generated locally.
2. **Models** - Run each model's notebook to perform hyperparameter search, nested cross-validation, filtering, and ensemble evaluation. Trained model weights are included in the repository, so this step can be skipped if you only intend to run XAI.
3. **XAI** - Run the XAI notebooks to apply the seven explainability methods to the selected model and fold. This step can be run directly using the trained models already provided.
