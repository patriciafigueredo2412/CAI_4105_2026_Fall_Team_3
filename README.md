# Apartment Rental Price Prediction

This project predicts the monthly rent of an apartment from its listing details and location. The notebook explores the supplied data, prepares the features, compares regression models, tunes selected models, chooses a final model using validation results, and creates predictions for the unlabeled test listings.

## Project files

- [`challenge_1/final_challenge1_corrected.ipynb`](challenge_1/final_challenge1_corrected.ipynb) — complete modeling workflow and prediction generation.
- [`challenge_1/challenge1_train.csv`](challenge_1/challenge1_train.csv) — 7,398 labeled training listings; the target column is `price`.
- [`challenge_1/challenge1_test.csv`](challenge_1/challenge1_test.csv) — 2,467 listings for final prediction; it has no target column.
- [`challenge_1/predictions.csv`](challenge_1/predictions.csv) — predicted `price` values paired with the test `ID` values.
- [`challenge_1/requirements.txt`](challenge_1/requirements.txt) — pinned Python package versions used to run the notebook.
- [`challenge_1/verification.json`](challenge_1/verification.json) — recorded validation results and checks for the prediction file.

## What the notebook does

1. Loads the training and test CSV files from the folder containing the notebook.
2. Examines the target and input variables. `ID` is retained to identify prediction rows but excluded from model features. The target is `price`.
3. Uses `bathrooms`, `bedrooms`, `square_feet`, `latitude`, `longitude`, `has_photo`, and `state` as the baseline predictors. It excludes `ID` and the nearly constant `category` field. The pipelines standardize numeric features and one-hot encode categorical features; later experiments also compare grouping rare states and target encoding state.
4. Adds engineered predictors: total rooms, square feet per room, bathrooms per bedroom, a studio indicator, log square feet, and latitude/longitude sum and difference. It also compares modeling `price` directly with modeling `log1p(price)` and converts transformed predictions back to the price scale for RMSE.
5. Evaluates Linear Regression, Decision Tree, Random Forest, and Gradient Boosting, as required, plus Ridge and Lasso regression.
6. Uses shuffled five-fold `GridSearchCV` to tune Decision Tree, Random Forest, Gradient Boosting, Ridge, and Lasso. It then searches a smaller regularization grid for Gradient Boosting, first with the original predictors and then with the engineered predictors.
7. Fits the selected engineered-feature Gradient Boosting pipeline using the labeled training data and writes `predictions.csv` for the test listings.

## Validation and model comparison

The primary metric is Root Mean Squared Error (RMSE), reported in the dataset's price units. Lower RMSE means predictions are closer to actual prices on average, with larger errors penalized more heavily. Training RMSE compared with validation RMSE helps show whether a model is overfitting or underfitting.

The notebook uses two training-data evaluation stages:

- An 80/20 split with `random_state=42` provides an initial comparison of the required models and additional linear models.
- Shuffled five-fold cross-validation with `random_state=42` evaluates later experiments and supplies the scores used during Grid Search and final model selection. Each pipeline fits its preprocessing using only the corresponding training fold.

The scores from these stages answer different questions and should not be compared as if they came from the same split. The initial holdout was not kept independent for the final tuned model because subsequent cross-validation used all labeled training rows. The final cross-validation score is therefore a model-selection estimate and may be optimistic after trying multiple configurations. The notebook also explains that its out-of-fold plots are diagnostics, not a nested cross-validation estimate of the full model-selection process. The hidden test prices are not used during development or tuning.

### Initial holdout results

| Model | Training RMSE | Holdout RMSE |
| --- | ---: | ---: |
| Random Forest | 159.57 | 453.32 |
| Gradient Boosting | 405.21 | 487.96 |
| Linear Regression | 515.14 | 550.04 |
| Lasso | 515.45 | 550.99 |
| Ridge | 515.47 | 551.42 |
| Decision Tree | 39.35 | 559.31 |

The much lower training errors for Random Forest and Decision Tree indicate overfitting in these baseline configurations. Linear models have a smaller training-to-holdout gap and are simpler to interpret, but their holdout RMSE is higher.

### Tuned cross-validation results

| Model | Default CV RMSE | Tuned CV RMSE | Tuned training RMSE | Train/CV gap |
| --- | ---: | ---: | ---: | ---: |
| Gradient Boosting | 461.37 | 392.90 | 261.83 | 131.07 |
| Random Forest | 423.33 | 413.61 | 177.38 | 236.23 |
| Decision Tree | 555.96 | 483.22 | 373.00 | 110.22 |
| Ridge | 526.77 | 526.72 | 521.08 | 5.64 |
| Lasso | 530.73 | 526.72 | 521.08 | 5.64 |

After tuning, the notebook further regularizes Gradient Boosting and evaluates engineered features. The selected engineered-feature Gradient Boosting model has a five-fold CV RMSE of **$385.05** with a fold standard deviation of **$25.45**. Its mean training RMSE is **$242.09**, leaving a **$142.96** training-to-validation gap, so some overfitting remains. The model has 800 trees, learning rate 0.05, maximum depth 5, minimum leaf size 10, `max_features=0.5`, and `subsample=1.0`. It was selected because it has the lowest reported CV RMSE among the evaluated final candidates. It can model nonlinear relationships between apartment attributes, location, and rent, though it takes more computation and is less directly interpretable than a linear model.

## Run the notebook

Use Python 3.12. From the repository root, create an environment and install the pinned dependencies:

### Windows PowerShell

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
$env:LOKY_MAX_CPU_COUNT = '4'
$env:OMP_NUM_THREADS = '1'
Set-Location challenge_1
python -m jupyterlab
```

### macOS or Linux

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
export LOKY_MAX_CPU_COUNT=4
export OMP_NUM_THREADS=1
cd challenge_1
python -m jupyterlab
```

In JupyterLab, open `final_challenge1_corrected.ipynb`, restart the kernel, and choose **Run All Cells**. Keep the two CSV files in `challenge_1` beside the notebook. The final cell writes `predictions.csv` in that folder. Grid Search can take a while to complete.

The notebook uses scikit-learn's `TargetEncoder` with a `KFold` object for one of its experiments; that interface requires scikit-learn 1.9 or newer. The pinned requirements provide the tested package versions.

## Prediction file

The submitted CSV contains exactly two columns, `ID` and `price`, with one row per test listing. The IDs preserve the order of the supplied test data, and the predicted prices are finite and positive. The saved predictions were checked against predictions from the fitted final model.

The recorded validation RMSE is not a hidden-test score or a guaranteed grade. Competition performance depends on the hidden prices and the other submissions; the assignment does not specify a fixed RMSE passing threshold.

## Team submission reminder

Submit the completed notebook, README, predictions file, and environment information with the team's GitHub work. Each team member must also submit their own `Peer-review form.docx` as instructed by the course.
