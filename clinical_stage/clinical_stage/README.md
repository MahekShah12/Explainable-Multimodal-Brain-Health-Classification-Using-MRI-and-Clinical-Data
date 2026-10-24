# OASIS-1 Clinical/Cognitive Stage — Stage 1

Clinical/cognitive-only analysis and baseline prediction of **CDR-based
cognitive status** from the OASIS-1 cross-sectional table. This is the
clinical half of a future multimodal (MRI + clinical) project — **no MRI
images or fusion model are included here.**

## How to run (VS Code / Windows)

1. Open this `clinical_stage` folder in VS Code.
2. Create/activate a virtual environment (optional but recommended).
3. Install dependencies:
   ```
   pip install pandas numpy matplotlib seaborn scikit-learn scipy joblib openpyxl
   ```
4. Run:
   ```
   python clinical_analysis.py
   ```
   The script reads `data\oasis_cross-sectional.xlsx` and regenerates every
   file listed below. The original Excel file is never modified.

## Folder structure

```
clinical_stage/
│
├── data/
│   ├── oasis_cross-sectional.xlsx     <- your original file (untouched)
│   └── clinical_cleaned.csv           <- rows with CDR present + engineered target
│
├── clinical_eda/                      <- 6 EDA plots (Step 6)
│
├── models/
│   └── clinical_rf_pipeline.joblib    <- fitted preprocessing+RF pipeline
│
├── outputs/
│   ├── step1_inspection_summary.txt
│   ├── step3_data_quality.txt
│   ├── step4_cdr_raw_distribution.csv
│   ├── step4_binary_target_distribution.csv
│   ├── step7_statistical_analysis.csv
│   ├── step11_model_comparison.csv
│   ├── step12_feature_importance.csv
│   ├── step14_final_summary.txt
│   ├── confusion_matrix_*.png (4 files)
│   └── feature_importance_random_forest.png
│
├── clinical_analysis.py
└── README.md
```

## Key decisions (see the accompanying chat explanation for full reasoning)

- **Target:** CDR, binarized into `0 = Nondemented` vs `1 = CDR > 0`
  (cognitively-impaired spectrum), because the CDR = 2 class has only 2
  subjects — too few for reliable multiclass/stratified evaluation.
- **Rows used for modeling:** only the 235/436 subjects who have a
  clinically recorded CDR. The other 201 are younger subjects who were
  never given a clinical dementia assessment — this is *not* random
  missingness, so those rows are excluded rather than imputed.
- **Dropped columns:** `ID` (identifier), `Hand` (constant — every subject
  is right-handed in this file), `Delay` (95%+ missing).
- **MMSE leakage caution:** MMSE is reported both included and excluded
  from the feature set, since it is conceptually close to the CDR label.
- Terminology throughout is **"CDR-based cognitive status"**, never
  "Alzheimer's diagnosis."

## Next stage (not implemented here)

```
MRI -> CNN / Transfer Learning -> MRI feature vector
Clinical data -> Clinical model -> Clinical feature vector
[MRI feature vector + Clinical feature vector] -> Feature Fusion -> Final Multimodal Model
```
