# ICH-Detection-Data-Partitioning
Code for comparative evaluation of patient-level and slice-level data partitioning strategies for deep learning-based intracranial hemorrhage detection in brain CT.
# Intracranial Hemorrhage Detection in Brain CT

This repository contains the research code used to evaluate deep learning-based intracranial hemorrhage (ICH) detection in brain CT under two different data-partitioning strategies: **patient-level splitting** and **slice-level splitting**.

The code accompanies the study:

**The Impact of Data Partitioning Strategy on Deep Learning Performance for Intracranial Hemorrhage Detection in Brain CT: A Comparative Evaluation of Slice-Level and Patient-Level Splitting**

## Experimental Overview

The study compares two independently implemented evaluation protocols while keeping the model architectures, preprocessing, training strategy, threshold selection, performance evaluation, and interpretability analysis as consistent as possible.

### Protocol P — Patient-Level Evaluation

The patient-level protocol uses:

* 45 patients in total
* 9 patients reserved as a fixed held-out test set

  * 4 hemorrhagic patients
  * 5 normal patients
* 36 patients used for model development

  * 14 hemorrhagic
  * 22 normal
* 4-fold patient-level stratified cross-validation
* 27 patients for training and 9 patients for validation within each fold
* Primary model-selection endpoint: patient-level AUROC
* Primary patient-level aggregation: mean slice probability
* Additional patient-level aggregations: top-10% mean and maximum probability
* Fixed random seed for reproducibility

The held-out test set is kept separate from model, epoch, and threshold selection.

### Protocol S — Slice-Level Evaluation

The slice-level protocol uses:

* 70% slice-level training data
* 15% slice-level validation data
* 15% slice-level held-out test data
* 4-fold slice-level stratified cross-validation within the training partition
* Fixed random seed of 42
* Patient-overlap auditing across partitions

Because this protocol intentionally performs splitting at the slice level, slices from the same patient may occur in different partitions. Patient-level aggregation of the test predictions is therefore treated as descriptive analysis rather than the primary evaluation.

## Deep Learning Models

Four CNN architectures are evaluated:

* MobileNetV2
* EfficientNet-B0
* ResNet-50
* DenseNet-121

MobileNetV2 and EfficientNet-B0 represent lightweight architectures, while ResNet-50 and DenseNet-121 represent larger conventional CNN architectures.

The models use ImageNet-based initialization and a binary classification head with dropout followed by a single output logit.

## Training Configuration

The main training configuration includes:

* Input image size: 224 × 224
* Batch size: 32
* Optimizer: Adam
* Learning rate: 1 × 10⁻⁴
* Weight decay: 1 × 10⁻⁴
* Dropout: 0.20
* Maximum epochs: 100
* Early-stopping patience: 10
* ReduceLROnPlateau learning-rate scheduling
* ImageNet normalization
* Patient-balanced weighted binary cross-entropy with logits
* Four-fold stratified cross-validation
* Fixed random seeds for reproducibility

## Performance Evaluation

The notebooks calculate multiple classification and efficiency measures, including:

* AUROC
* AUPRC
* Accuracy
* Precision
* Sensitivity
* Specificity
* F1-score
* Confusion matrices
* Model parameter counts
* CPU/GPU inference latency
* FLOPs/MACs when available

The experiments also include bootstrap-based confidence intervals using patient-level clustering where applicable.

## Threshold Analysis

In addition to the primary classification threshold, the analysis includes development-set threshold selection targeting a specificity of 0.90.

The held-out test data are not used for selecting the threshold.

## Interpretability Analysis

Grad-CAM is used to investigate the image regions contributing to the positive hemorrhage prediction.

The repository includes:

* Grad-CAM generation
* Architecture-specific target layers
* Saliency visualization
* Grad-CAM deletion/insertion faithfulness analysis

Grad-CAM is used as an interpretability method and **not as a lesion-segmentation method**. No Dice or IoU measurements are calculated because validated lesion masks are not available.

## Dataset

The experiments use the Brain CT Hemorrhage Dataset available through Kaggle.

The dataset is not included in this repository. Users should obtain the dataset from its original source and configure the dataset paths in the notebooks accordingly.

## Repository Contents

```text
ICH-Detection-Data-Partitioning/
│
├── README.md
│
├── patient_level_protocol.ipynb
│
└── slice_level_protocol.ipynb
```

The two notebooks contain the complete experimental pipelines for the patient-level and slice-level protocols.

Reproducibility

To reproduce the experiments:

1. Obtain the dataset from its original source.
2. Configure the dataset paths in the corresponding notebook.
3. Install the required Python packages.
4. Configure a CUDA-enabled environment if GPU execution is desired.
5. Run the patient-level and slice-level notebooks independently.
6. Generated results, figures, model checkpoints, Grad-CAM outputs, and tabular results are saved by the respective pipelines.

