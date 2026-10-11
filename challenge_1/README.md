# Challenge 1: Rental Price Prediction

This folder contains the data and notebook for predicting rental listing prices. The training data includes the target column `price`; the test data does not.

## Project files

- `data/challenge1_train.csv` — labeled training data.
- `data/challenge1_test.csv` — unlabeled listings for prediction.
- `notebooks/challenge1_rental_pricing.ipynb` — data exploration, preprocessing, and model evaluation.

The `ID` column is retained for identifying test predictions and excluded from the model features. The target is `price`.

## Setup

Run these commands from the repository root. They create a virtual environment and install the packages used by the notebook.

### Windows PowerShell

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install numpy pandas scikit-learn matplotlib seaborn jupyter
jupyter notebook challenge_1/notebooks/challenge1_rental_pricing.ipynb
```

### macOS or Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install numpy pandas scikit-learn matplotlib seaborn jupyter
jupyter notebook challenge_1/notebooks/challenge1_rental_pricing.ipynb
```

## Preprocessing and validation

The baseline uses a scikit-learn `ColumnTransformer` inside each model pipeline. Numeric features (`bathrooms`, `bedrooms`, `square_feet`, `latitude`, and `longitude`) are median-imputed and standardized. Categorical features (`category`, `has_photo`, and `state`) are imputed with the most frequent value and one-hot encoded; unseen categories are ignored. Keeping preprocessing in the pipeline means it is fitted separately within each training fold.

Models are compared with shuffled 5-fold cross-validation (`random_state=42`). The evaluation reports Train RMSE and Validation RMSE, including the mean and standard deviation of validation RMSE. The overfit gap is mean Validation RMSE minus mean Train RMSE. RMSE is computed from scikit-learn's negative mean-squared-error scores.

## Baseline results

The following results were recorded for the five baseline models on Patricia's challenge branch. RMSE is in the dataset's price units.

| Model | Train RMSE | Validation RMSE (mean ± std) | Gap |
| --- | ---: | ---: | ---: |
| Random Forest | 162.97 | 426.51 ± 33.60 | 263.54 |
| Gradient Boosting | 406.71 | 460.36 ± 28.09 | 53.65 |
| Linear Regression (Normal Equation) | 520.87 | 526.71 ± 25.23 | 5.85 |
| Linear Regression (scikit-learn) | 520.87 | 526.76 ± 25.27 | 5.89 |
| Decision Tree | 40.67 | 562.27 ± 65.26 | 521.60 |

Random Forest has the lowest baseline validation error, but its much lower training error indicates a substantial train-validation gap. Gradient Boosting has a somewhat higher validation error and a smaller gap, giving it a more balanced baseline profile. Both linear-regression implementations have nearly identical results and the smallest gaps; linear regression is also the simplest and most interpretable option, though its validation error is higher. The unbounded baseline Decision Tree nearly fits the training data and performs worst on validation, which is strong evidence of overfitting. The tree ensembles can model nonlinear relationships but are less directly interpretable than linear regression.

## Model selection status

These are baseline results, not the final model comparison. The final recommendation is pending the tuned-model results and Bianca's results. Once both are available, compare their validation RMSE and train-validation gaps alongside model complexity and interpretability, then update this section with the selected model and its justification.
