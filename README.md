# AI for Phishing Email Detection

This project implements a controlled multi-modal phishing email detection experiment in Google Colab. It compares two model branches:

1. **TF-IDF + URL features**
2. **Frozen DistilRoBERTa embeddings + URL features**

Both branches use the same URL feature set, Logistic Regression classifier family, train-test split, and evaluation metrics so that the main comparison is the text representation method.

## Main file

Run the following notebook:

```text
5564197_phishing_project.ipynb
```

## Recommended environment

This notebook is designed to run in **Google Colab**.

Recommended runtime:

```text
Runtime type: Python 3
Hardware accelerator: GPU
```

A GPU is strongly recommended because the DistilRoBERTa embedding extraction stage is computationally expensive. The notebook can still run on CPU, but it will take much longer.

## Required Python packages

The notebook installs or imports the required libraries automatically. The main dependencies are:

```text
pandas
numpy
scikit-learn
torch
transformers
tqdm
beautifulsoup4
seaborn
matplotlib
joblib
scipy
```

## How to run in Google Colab

1. Upload `5564197_phishing_project.ipynb` to Google Colab.
2. Select a GPU runtime:
   - `Runtime` → `Change runtime type` → `Hardware accelerator` → `GPU`.
3. Run the notebook from top to bottom:
   - `Runtime` → `Run all`.
4. When prompted, allow Google Drive access if `SAVE_TO_DRIVE = True`.
5. Wait for the dataset download, preprocessing, feature extraction, model training, evaluation, and saving steps to complete.

## Output configuration

The notebook uses the following default paths:

```python
BASE_OUTPUT_DIR = "/content/phishing_project"
DATASETS_DIR = "/content/phishing_project/data/datasets"
OUTPUTS_DIR = "/content/phishing_project/outputs"
DRIVE_OUTPUTS_DIR = "/content/drive/MyDrive/phishing_project_outputs"
SAVE_TO_DRIVE = True
```

If `SAVE_TO_DRIVE = True`, outputs are saved to Google Drive as well as the Colab runtime. This is recommended because Colab local files are temporary and may be lost when the session ends.

If you do not want to use Google Drive, set:

```python
SAVE_TO_DRIVE = False
```

before running the notebook.

## Dataset acquisition

The notebook downloads selected CSV files from the public Zenodo **Phishing Email Curated Datasets** collection.

The selected datasets are:

```text
Enron.csv
Ling.csv
Nazario.csv
Nazario_5.csv
Nigerian_5.csv
Nigerian_Fraud.csv
SpamAssasin.csv
TREC_05.csv
TREC_06.csv
TREC_07.csv
CEAS_08.csv
```

The notebook normalises these files into a common schema:

```text
text
label
source_dataset
```

Labels are represented as:

```text
0 = legitimate
1 = phishing
```

## What the notebook does

The notebook performs the following stages:

1. Configures output folders and Google Drive saving.
2. Downloads the selected public phishing email datasets.
3. Installs/imports required libraries.
4. Loads and normalises the datasets.
5. Deduplicates emails using text fingerprints.
6. Balances the legitimate and phishing classes.
7. Extracts URL-based structural features.
8. Performs an 80:20 stratified train-test split.
9. Trains and evaluates a TF-IDF + URL Logistic Regression model.
10. Extracts or loads frozen DistilRoBERTa mean-pooled embeddings.
11. Trains and evaluates a DistilRoBERTa + URL Logistic Regression model.
12. Runs McNemar's test on paired held-out predictions.
13. Generates confusion matrices and error breakdowns.
14. Runs repeated stratified robustness testing.
15. Runs a CEAS_08 source-held-out generalisation check.
16. Saves final results, model artefacts, and output CSV files.

## Main outputs

The notebook saves outputs in:

```text
/content/phishing_project/outputs
```

and, if Google Drive saving is enabled:

```text
/content/drive/MyDrive/phishing_project_outputs
```

Expected output files include:

```text
phishing_detection_results.csv
phishing_detection_error_breakdown.csv
phishing_detection_cost_comparison.csv
phishing_detection_mcnemar_test.csv
phishing_detection_repeated_split_summary_subset20000.csv
phishing_detection_source_heldout_results.csv
heldout_predictions.csv
```

The notebook also saves trained artefacts such as:

```text
tfidf_vectorizer.joblib
tfidf_url_scaler.joblib
tfidf_logreg_model.joblib
distilroberta_scaler.joblib
distilroberta_url_scaler.joblib
distilroberta_logreg_model.joblib
```

These are stored in the `saved_artefacts` output folder.

## DistilRoBERTa embedding caching

The DistilRoBERTa branch can take a long time when embeddings are extracted from scratch. To reduce rerun time, the notebook saves `.npy` embedding files to Google Drive when `SAVE_TO_DRIVE = True`.

On later runs, the notebook checks whether saved embeddings already exist and whether their shapes match the current train-test split. If they match, the notebook loads them instead of re-extracting them.

This means later runs may be faster than the first full run.

## Reproducibility

The notebook sets a fixed random seed:

```python
RANDOM_STATE = 42
```

This supports reproducible train-test splits, sampling, and model evaluation. Some runtime values may still vary slightly because of Colab resource allocation, GPU availability, Google Drive caching, and parallel grid-search execution.

## Running locally instead of Colab

The project is intended for Colab, but it can be adapted for local execution.

To run locally:

1. Install the required Python packages.
2. Set `SAVE_TO_DRIVE = False`.
3. Change `BASE_OUTPUT_DIR` to a local path, for example:

```python
BASE_OUTPUT_DIR = "./phishing_project"
```

4. Run the notebook in Jupyter Notebook or JupyterLab.

Local execution is not recommended unless the machine has sufficient memory and ideally a CUDA-enabled GPU.

## Notes

- The project uses public secondary datasets only.
- No live email systems, private inboxes, scraping, or API-based collection are used.
- The notebook is intended for defensive research and evaluation of phishing detection methods.
