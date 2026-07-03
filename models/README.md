# Models

This module contains the five GNN models trained for binary CN vs MCI classification. Each model is built around a different graph convolution operator, but all share the same overall training strategy, evaluation protocol, and filtering pipeline.

The five models are:
- **NNConv** - Edge Conditioned Convolution
- **TransformerConv** - Graph Transformer
- **PNAConv** - Principal Neighbourhood Aggregation
- **GINEConv** - Graph Isomorphism Network with Edge features
- **ChebConv** - Chebyshev Spectral Convolution

---

## Training strategy

Training proceeds in three stages, built around a nested cross-validation scheme.

### Stage 1 - Inner filtering (nested 3-fold CV)
For each model, a hyperparameter search is conducted, producing a set of candidate architectures. All candidates are trained and evaluated using a nested 3-fold cross-validation on the training set (i.e. trained on 2/3, validated on the remaining 1/3). Candidates are then filtered based on their mean performance across the 3 inner folds, using minimum thresholds on three metrics: MCI recall, CN recall, and balanced accuracy. Thresholds are set per model. Architectures that fail to meet all three thresholds are discarded.

### Stage 2 - Outer filtering (5-fold CV)
Surviving architectures are re-trained from scratch on the full training set of each outer fold and evaluated on the corresponding outer validation set. The same three-metric filtering is applied again using per-model thresholds, this time based on mean performance across the 5 outer folds. This constitutes the outer filtering step. Once again, architectures that fail to meet all three thresholds are discarded.

### Stage 3 - Test evaluation & ensemble
Architectures that survive both filtering stages are evaluated on the held-out test set of each outer fold. Final metrics are reported per fold and averaged across folds. Additionally, for each model, a **majority voting ensemble** is constructed from its surviving architectures, producing a single aggregated prediction per fold.

---

## Results

The table below reports ensemble performance for each model, averaged across the 5 outer folds.

| Model | Bal. Accuracy | Recall MCI | Recall CN | AUC | F1-macro | Accuracy |
|---|---|---|---|---|---|---|
| GINEConv | **0.696** | 0.595 | **0.798** | 0.764 | **0.667** | **0.761** |
| TransformerConv | 0.677 | 0.660 | 0.695 | 0.748 | 0.620 | 0.690 |
| NNConv | 0.675 | 0.604 | 0.746 | **0.767** | 0.636 | 0.720 |
| PNAConv | 0.665 | **0.761** | 0.569 | 0.744 | 0.566 | 0.606 |
| ChebConv | 0.652 | 0.615 | 0.690 | 0.739 | 0.599 | 0.679 |

## Saved models

Trained model weights for all surviving architectures are saved under `results/`, organised per model:

```
results/
├── chebconv_results/
├── gineconv_results/
├── nnconv_results/
├── pna_results/
└── transformer_results/
```

These can be loaded directly to skip retraining and run XAI analysis.