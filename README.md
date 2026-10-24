# Explainable Multimodal Brain Health Classification Using MRI and Clinical Data

## 1. Project Overview

This project explores a multimodal machine learning framework for brain health classification using brain MRI images and clinical data from the OASIS-1 dataset.

The system combines deep learning models for MRI analysis with a Random Forest classifier for clinical features. Their predicted probabilities are combined using weighted fusion to classify subjects into two CDR-based categories.

The project also investigates model interpretability through Grad-CAM for MRI models and feature importance analysis for the clinical model.

**Note:** This is an academic research project and is not a clinically validated diagnostic tool.

## 2. Objectives

- Develop and compare CNN models for MRI-based classification.
- Build a clinical prediction model using selected demographic and brain-volume features.
- Combine MRI and clinical predictions using weighted probability fusion.
- Evaluate model performance using standard classification metrics.
- Investigate model interpretability using Grad-CAM and clinical feature importance.
- Provide an interactive dashboard for exploring model predictions.

## 3. Dataset

This project uses the **OASIS-1 (Open Access Series of Imaging Studies)** dataset, which contains cross-sectional brain MRI scans and associated demographic, clinical, and brain-volume information.

### Official dataset links

- **OASIS-1 dataset and download instructions:** https://sites.wustl.edu/oasisbrains/home/oasis-1/
- **OASIS data access information:** https://sites.wustl.edu/oasisbrains/home/access/
- **Original dataset publication:** https://doi.org/10.1162/jocn.2007.19.9.1498

The official website provides access to MRI data and clinical/demographic resources. Follow the applicable access instructions and data-use terms.

### Clinical features

The clinical model uses seven input features:

| Feature | Meaning |
|---|---|
| Age | Participant's age in years |
| Educ | Years of education, as encoded in the dataset |
| SES | Socioeconomic status category |
| eTIV | Estimated total intracranial volume |
| nWBV | Normalized whole-brain volume |
| ASF | Atlas scaling factor |
| Sex | Recorded sex category |

MMSE (Mini-Mental State Examination) is excluded from the primary clinical prediction workflow.

### Target definition

The project formulates classification using the Clinical Dementia Rating (CDR):

- **Class 0:** CDR = 0
- **Class 1:** CDR > 0

This is a binary CDR-based classification task and should not be interpreted as definitive clinical diagnosis of Alzheimer's disease.

## 4. Project Architecture

The framework contains two main branches.

### MRI branch

MRI images are preprocessed and supplied to the CNN models:

- ResNet50
- DenseNet121
- EfficientNetB0
- EfficientNetV2B0

The image preprocessing pipeline uses a target image size of 224 × 224 pixels. The trained models generate MRI-based prediction probabilities, which are aggregated at subject level where supported by the evaluation pipeline.

### Clinical branch

Selected clinical features are prepared and supplied to a trained Random Forest classifier. The model produces a clinical prediction probability.

### Multimodal fusion

The MRI and clinical probabilities are combined using weighted fusion:

`P_fusion = 0.40 × P_MRI + 0.60 × P_clinical`

The configured decision threshold is 0.43:

- If `P_fusion ≥ 0.43`, predict Class 1.
- If `P_fusion < 0.43`, predict Class 0.

These weights and the threshold correspond to the project's configured fusion pipeline. Refer to the implementation and saved evaluation results when reproducing experiments.

## 5. Methodology

The main workflow consists of:

1. Inspecting MRI files and clinical metadata.
2. Cleaning metadata and checking subject labels.
3. Preparing MRI images and clinical features.
4. Creating subject-wise training, validation, and testing splits.
5. Training and evaluating multiple CNN architectures.
6. Training the clinical Random Forest model.
7. Aggregating MRI predictions at subject level where applicable.
8. Combining MRI and clinical probabilities.
9. Evaluating classification performance.
10. Examining model explanations and prediction errors.

The exact preprocessing operations, training settings, and data splits should be taken from the corresponding scripts and configuration used for the final experiment.

## 6. Evaluation Metrics

The project uses standard classification metrics, including:

- **Accuracy:** Overall proportion of correct predictions.
- **Precision:** Proportion of positive predictions that are correct.
- **Recall:** Proportion of actual positive subjects identified.
- **F1-score:** Harmonic mean of precision and recall.
- **ROC-AUC:** Ability to distinguish between the two classes across decision thresholds.
- **Confusion matrix:** Counts of correct and incorrect predictions for each class.

Performance should be compared using results generated from the same evaluation split and prediction level.

## 7. Explainability

### Grad-CAM

Gradient-weighted Class Activation Mapping (Grad-CAM) can highlight image regions that contribute to a CNN prediction. These heatmaps help inspect model attention but do not independently establish clinical validity.

### Clinical feature importance

The clinical model's feature-importance analysis ranks the input variables according to their contribution to the fitted Random Forest. Feature importance indicates model behaviour and should not be interpreted as proof of causation.

## 8. Repository Structure

The repository contains scripts and resources for the MRI, clinical, fusion, and dashboard workflows.

```text
HA_DATASET/
├── code/
│   ├── fusion/
│   ├── clean_metadata.py
│   ├── prepare_splits.py
│   ├── train_cnn.py
│   ├── train_finetune.py
│   ├── evaluate_ensemble.py
│   ├── subject_level_evaluation.py
│   ├── gradcam_analysis.py
│   └── threshold_optimization.py
├── clinical_stage/
│   └── clinical_stage/
├── dashboard/
│   └── app.py
├── OASIS_CNN_Dataset_1_12/
│   ├── image_metadata.csv
│   ├── train_metadata.csv
│   ├── validation_metadata.csv
│   ├── test_metadata.csv
│   └── model_results/
├── requirements.txt
├── .gitignore
└── README.md
```

This is an illustrative overview. The actual repository may contain additional scripts, metadata files, model checkpoints, and results.

## 9. Installation

### Prerequisites

- Python
- pip
- Git
- Required Python packages listed in `requirements.txt`

Clone the repository:

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
cd HA_DATASET
```

Create and activate a virtual environment.

**Windows PowerShell:**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install dependencies:

```bash
pip install -r requirements.txt
```

If the project uses different dependency versions or requires additional setup, follow the project's actual environment configuration.

## 10. Dataset Setup

MRI images are excluded from the GitHub repository to avoid uploading large dataset files.

1. Visit the official OASIS-1 website.
2. Follow the applicable dataset access and download instructions.
3. Download and prepare the required MRI data and clinical metadata.
4. Place the files in the locations expected by the preprocessing scripts.
5. Check the metadata paths and subject identifiers before running the pipeline.

**Important:** Metadata CSV files alone are not sufficient to train the MRI models. The corresponding MRI images must also be available locally.

## 11. Running the Project

Run the scripts from the project directory and check their arguments and expected file paths before execution.

Typical workflow:

```bash
python code/01_check_data.py
python code/clean_metadata.py
python code/prepare_splits.py
python code/train_cnn.py
python code/evaluate_ensemble.py
python code/subject_level_evaluation.py
```

The exact sequence may differ depending on the selected experiment. Some scripts require trained models, preprocessed metadata, or additional command-line arguments.

### Launching the dashboard

If Streamlit is installed and `dashboard/app.py` is the configured entry point:

```bash
streamlit run dashboard/app.py
```

Ensure that the model files and data paths required by the dashboard are available before launching it.

## 12. Results

The repository includes evaluation outputs, model comparisons, ROC curves, confusion matrices, and clinical feature-importance visualizations where generated by the experiments.

Use the saved results under the relevant `model_results/` and `code/fusion/` directories to report the final metrics. Results should identify the evaluation split, classification threshold, and whether predictions are image-level or subject-level.

Do not interpret validation results as test results or compare metrics from different splits as if they were directly equivalent.

## 13. Limitations

- The experiments depend on the available labeled subjects and data quality.
- Missing clinical labels may limit the usable sample size.
- Model performance may vary with data splits and decision thresholds.
- Grad-CAM and feature importance provide limited forms of interpretability.
- Independent external validation is required before considering any clinical use.

## 14. Reproducibility and Responsible Use

- Preserve subject-wise separation between training, validation, and testing.
- Fit preprocessing transformations using training data where appropriate.
- Avoid target leakage when selecting clinical predictors.
- Keep track of model configurations, thresholds, and evaluation outputs.
- Do not upload restricted data, credentials, or private information to the repository.
- Verify data access terms before sharing the dataset or derived files.

## 15. Acknowledgements and Citation

The project uses the OASIS-1 dataset. Please acknowledge the dataset according to the official OASIS data-use requirements.

**Reference**

Marcus, D. S., Wang, T. H., Parker, J., Csernansky, J. G., Morris, J. C., and Buckner, R. L. “Open Access Series of Imaging Studies (OASIS): Cross-sectional MRI Data in Young, Middle Aged, Nondemented and Demented Older Adults.” *Journal of Cognitive Neuroscience*, 19(9), 1498–1507, 2007.

https://doi.org/10.1162/jocn.2007.19.9.1498
mujhe poora 