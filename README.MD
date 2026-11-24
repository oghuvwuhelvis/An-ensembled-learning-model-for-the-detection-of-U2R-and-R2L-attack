# An Improved Ensemble Learning Model for the Detection of U2R and R2L Attacks

This repository contains the notebook and dataset used to develop and evaluate machine-learning models for detecting minority attack categories in the KDD Cup 1999 network-intrusion dataset.

The study compares Random Forest, XGBoost, LightGBM and Logistic Regression with a stacking ensemble. Particular attention is given to User-to-Root (U2R) and Remote-to-Local (R2L) attacks because these categories contain substantially fewer records than Normal, Denial of Service (DoS) and Probe traffic.

## Research question

How effectively can an improved stacking ensemble enhance the detection of U2R and R2L attacks in the KDD'99 dataset compared with individual classifiers?

## Repository contents

| File | Purpose |
|---|---|
| `U2R_R2L_IDS_Detection.ipynb` | Data preparation, model training, evaluation and comparison |
| `kddcup99_csv.csv.zip` | Compressed KDD'99 dataset used by the project |
| `requirements.txt` | Python packages required by the notebook |

## Method

The retained notebook performs the following stages:

1. Load and inspect the prepared KDD'99 data.
2. Map individual attack labels into Normal, DoS, Probe, R2L and U2R categories.
3. Split the data into stratified training and test sets using a fixed random seed.
4. Encode categorical variables and scale or select features where required.
5. Apply the Synthetic Minority Oversampling Technique (SMOTE) to the training data.
6. Train the individual classifiers and stacking ensemble.
7. Compare accuracy, macro precision, macro recall, macro F1-score and class-specific performance.

## Final retained results

The following values come from the final Phase 4 outputs retained in the notebook.

| Model | Accuracy | Macro precision | Macro recall | Macro F1-score |
|---|---:|---:|---:|---:|
| Random Forest | 99.9322% | 94.3916% | 90.1441% | 92.0863% |
| XGBoost | 99.9271% | 84.3390% | 94.9242% | 87.4387% |
| Logistic Regression | 98.5527% | 63.1784% | 97.1108% | 69.2614% |
| LightGBM | 99.9494% | 91.4924% | 94.6336% | 92.8174% |
| Stacking ensemble | 99.9474% | 91.6401% | 94.4566% | 92.7955% |

Minority-class results for the stacking ensemble were:

| Attack category | Precision | Recall | F1-score | Test support |
|---|---:|---:|---:|---:|
| U2R | 61.54% | 80.00% | 69.57% | 10 |
| R2L | 97.66% | 92.89% | 95.22% | 225 |

LightGBM recorded the marginally highest overall accuracy and macro F1-score. LightGBM and stacking jointly achieved the highest U2R F1-score. These findings should be interpreted alongside the small U2R test support and the age and known limitations of KDD'99.

## Installation

Create and activate a virtual environment, then install the dependencies:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

macOS or Linux:

```bash
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## Prepare the dataset

The notebook expects a file named `train_cleaned.csv` in the repository root. The supplied archive contains `kddcup99_csv.csv`. Extract the archive and run the following once before opening the notebook:

```python
import pandas as pd

df = pd.read_csv("kddcup99_csv.csv")
df = df.rename(columns={
    "label": "connection_type",
    "lnum_compromised": "num_compromised",
    "lroot_shell": "root_shell",
    "lsu_attempted": "su_attempted",
    "lnum_root": "num_root",
    "lnum_file_creations": "num_file_creations",
    "lnum_shells": "num_shells",
    "lnum_access_files": "num_access_files",
})

if "lnum_outbound_cmds" in df.columns:
    df = df.drop(columns=["lnum_outbound_cmds"])

df.to_csv("train_cleaned.csv", index=False)
```

The resulting file contains the 40 predictor columns and the `connection_type` target expected by the notebook.

## Run the notebook

Start Jupyter from the repository directory:

```bash
jupyter notebook U2R_R2L_IDS_Detection.ipynb
```

Run the cells in order from the beginning. Model training can require substantial memory and processing time because the dataset contains 494,021 records and the final models include SMOTE, gradient boosting and cross-validated stacking.

## Interpretation and limitations

- High overall accuracy partly reflects the strong representation of the majority classes.
- U2R performance is based on only 10 test records in the retained split, so small changes in predictions can produce large changes in percentage-based measures.
- KDD'99 is a historical benchmark and does not represent the full range of contemporary network behaviour or attacks.
- The results describe an experimental model evaluation. The notebook is not a deployed or production-ready intrusion-detection system.
- Performance should be assessed with class-specific precision, recall and F1-score rather than accuracy alone.

## Responsible use

This repository is provided for academic research and model evaluation. It should not be treated as a substitute for a validated operational security system. Users remain responsible for checking the dataset licence and attribution requirements before redistributing the data.

## Author

Elvis Oghuvwuh  
MSc Big Data Analytics  
Sheffield Hallam University

