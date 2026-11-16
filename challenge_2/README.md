# Challenge 2 — Online Shopping Purchase Prediction

This project predicts whether an online shopping session ends in a purchase (`Revenue=True`). The supplied training set has 9,247 labeled sessions, about 15.5% of which ended in a purchase. The 3,083 unlabeled test sessions are reserved for final predictions. `ID` identifies each submission row and is excluded from the predictors.

## Approach and validation

The notebook standardizes numeric features, one-hot encodes categorical features, and passes the binary `Weekend` flag through. Preprocessing is fitted inside each training fold. Feature engineering adds session totals, browsing ratios, and an indicator for nonzero page value.

Models are compared with shuffled five-fold stratified cross-validation using seed 42. The primary metric is F1 for `Revenue=True`. The notebook also reports precision, recall, ROC AUC, training versus validation F1, and confusion matrices for selected baselines.

| Model | Baseline mean CV F1 | With engineered features |
| --- | ---: | ---: |
| Logistic Regression | 0.5152 | 0.6455 |
| Gaussian Naïve Bayes | 0.2985 | 0.3013 |
| K-Nearest Neighbors | 0.4940 | 0.5305 |
| Support Vector Machine | 0.5962 | 0.6436 |
| Decision Tree | 0.5648 | 0.5560 |
| Random Forest | 0.6273 | 0.6634 |
| Gradient Boosting | 0.6612 | 0.6642 |

## Final model

Grid Search tuned Random Forest and Gradient Boosting with the engineered features. The selected Random Forest scored **0.6983 mean CV F1**, ahead of tuned Gradient Boosting at **0.6730**. Its training F1 was **0.9154**, so a train–validation gap remained after tuning.

The selected Random Forest uses 300 trees, a maximum depth of 15, at least three samples per leaf and four per split, `max_features=0.5`, and `class_weight='balanced_subsample'`. On pooled out-of-fold predictions, F1 was **0.6986** at the default threshold of 0.500 and **0.6999** at the selected threshold of 0.510. The same pooled predictions were used to choose and score the threshold, so 0.6999 is a tuning result.

The notebook refits the selected pipeline on all labeled sessions and predicts `True` when the purchase probability is at least 0.510. It writes [`predictions.csv`](predictions.csv) with `ID` and `Revenue` for every test session.

## Run the notebook

The saved notebook records Python 3.13.9. Start from the `challenge_2` directory with that Python version active. Create and activate a virtual environment, then install the notebook's dependencies.

```text
python -m venv .venv
```

On Windows PowerShell, activate it with `.\.venv\Scripts\Activate.ps1`. On macOS or Linux, use `source .venv/bin/activate`.

```text
python -m pip install jupyterlab numpy pandas matplotlib seaborn scikit-learn
python -m jupyterlab
```

Open [`Challenge2.ipynb`](Challenge2.ipynb), select the Python kernel for this environment, and run all cells with `challenge2_train.csv` and `challenge2_test.csv` beside it. Grid Search can take a while. The final cell regenerates `predictions.csv` in the same directory. The install command resolves currently compatible versions. The saved notebook does not record the original package versions.
