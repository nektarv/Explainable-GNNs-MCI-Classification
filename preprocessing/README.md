# Preprocessing

This module handles all preprocessing applied to the OASIS-3 dataset prior to model training. The pipeline operates on two data modalities: volumetric features derived from T1-weighted MRI scans (sMRI), and structural connectivity graphs derived from DTI scans.

The dataset contains three diagnostic classes: Cognitively Normal, Mild Cognitive Impairment, and Dementia. The Dementia class was dropped due to a very small sample size, making this a binary CN vs MCI classification task.

---

## Fold-aware preprocessing

To ensure unbiased evaluation, all preprocessing is applied **per fold** and fitted **exclusively on the CN samples of the training set**. This applies to covariate correction, normalization, and graph feature scaling. Validation and test sets are transformed using the statistics derived from the training set only.

The dataset is split into 5 folds (80/20 train+val / test). Each training set is further split 85/15 into train / validation. A nested 3-fold cross-validation is then applied within the training set for hyperparameter tuning.

---

## T1 processing

### Feature removal
A set of volumetric features is removed prior to any further processing, targeting either structures that are too small to be reliable, or aggregate measurements that are already represented by more granular individual features:

`5th-Ventricle_volume`, `CSF_volume`, `CortexVol`, `CorticalWhiteMatterVol`, `IntraCranialVol`, `Optic-Chiasm_volume`, `SubCortGrayVol`, `SupraTentorialVol`, `TotalGrayVol`, `WM-hypointensities_volume`, `lhCortexVol`, `lhCorticalWhiteMatterVol`, `non-WM-hypointensities_volume`, `rhCortexVol`, `rhCorticalWhiteMatterVol`, `Right-non-WM-hypointensities_volume`, `Left-non-WM-hypointensities_volume`, `Right-WM-hypointensities_volume`, `Left-WM-hypointensities_volume`, `L.Numvert`, `R.NumVert`, `L.SurfArea`, `R.SurfArea`, `TOTAL_HIPPOCAMPUS_VOLUME`

### Covariate correction
Many volumetric measurements are influenced by confounding biological factors - age, sex, and total brain volume - that are not themselves diagnostic indicators of MCI. A linear regression model is trained on the CN samples of the training set to capture this influence, and each feature's corrected value is computed as the residual between its original value and the model's prediction.

Two separate models are used to account for the non-linear relationship between brain measurements and age: one for the 42–69 age range, and one for the 70–96 range. For cortical thickness features specifically, only age and sex are used as predictors (brain volume is excluded).

### Normalization
After covariate correction, all remaining features are standardized using the mean and standard deviation of the CN training samples.

---

## DTI processing

### Feature removal
The `fiber_density` edge feature is dropped, as the dataset already contains the equivalent normalized version `normalized_fiber_density`.

### Normalization
The remaining 15 edge features are standardized using the mean and standard deviation of the CN training samples.

### Self-loop removal
Self-loops (edges from a node to itself) are removed from all graphs.

---

## Graph construction

The preprocessed sMRI volumetric features are fused onto the DTI graphs as node features. Each node represents a brain region, and its volumetric measurements are added as node-level attributes. Two binary mask features are also added per node, indicating whether sMRI and DTI features are both present for that node (used downstream by the models).

The final graphs for each fold - training, validation, and test sets - are saved separately. The internal train/validation split for the nested CV is also pre-computed and stored, ensuring identical splits across all models.
