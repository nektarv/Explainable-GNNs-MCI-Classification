# XAI

This module contains the explainability analysis applied to the trained GNN models. Seven XAI techniques are used to interpret model predictions and identify which brain regions and connectivity patterns the model relies on when distinguishing CN from MCI patients.

## Model

All seven methods are applied to a single model: **NNConv `config00`** on **fold 1**, selected as the best performing single-fold instance across all models and configurations (hyperparameters: `mci_weight=3.7`, `gamma=1.0`, `lr=0.001`, `wd=0.0005`, `hidden=32`).

---

## Methods

### Graph structure methods
**GNNExplainer** and **PGExplainer** both work by learning a soft importance mask directly over the graph structure. Rather than examining individual node features, they identify which edges - i.e. white matter pathways connecting pairs of brain regions - are most relevant to the model's prediction. GNNExplainer learns this mask locally for each individual sample, while PGExplainer learns a global parametric explainer that generalises across samples.

### Gradient-based methods
**Graph Grad-CAM** uses the gradients of the model's output with respect to intermediate node activations, producing a saliency map over brain regions - highlighting which areas activated the model most strongly.

**SmoothGrad** reduces the noise inherent in raw gradient signals by averaging gradients over multiple passes with added input noise. It provides both node-level and feature-level importance.

**Guided Backpropagation** is also gradient-based but operates at the input level, producing cleaner attribution maps by suppressing negative gradient contributions. It distinguishes features that push the prediction toward the target class from those that suppress it.

**Integrated Gradients** takes a more theoretically grounded approach, accumulating gradients along a path from a neutral baseline to the actual input. It satisfies formal attribution axioms and provides importance scores for both node and edge features.

### Counterfactual explanations
**Counterfactual Explanations** follow a fundamentally different logic from the methods above. Rather than explaining what the model uses to make a prediction, they search for the minimal change to the input that would flip the prediction. In this context, they answer a clinically meaningful question: what would need to change biologically for a patient to transition from one diagnostic class to the other.

---

## Findings

Results are broadly consistent with the existing neuroscientific literature on MCI. Despite the fundamentally different philosophies underlying the methods findings converge on a common pattern. For MCI patients, the model relies primarily on medial temporal lobe structures, most notably the hippocampus, entorhinal cortex, parahippocampal cortex, and thalamo-hippocampal regions. For CN patients, the model appears to recognise a broader fronto-limbic network, emphasising the connectivity between the anterior cingulate and the amygdalae, as well as the preservation of frontal and subcortical circuits.