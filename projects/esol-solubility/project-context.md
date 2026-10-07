# ESOL Solubility Project Context

## Project Goal

Learn the basic workflow of molecular machine learning using aqueous solubility prediction as the first AI4S project.

The main purpose is not only to obtain good prediction performance, but to understand the complete workflow:

data → molecular representation → train/validation/test split → model → evaluation → error analysis → scientific interpretation.

---

## Dataset

Dataset:

ESOL / Delaney aqueous solubility dataset

Target:

logS

The dataset is used as an introductory molecular property prediction task.

---

## Current Tools

Main environment:

- Google Colab
- Python
- DeepChem
- RDKit
- scikit-learn
- NumPy
- pandas
- matplotlib

---

## Molecular Representation

Current representation:

Circular fingerprint

Current settings:

```python
CircularFingerprint(
    radius=2,
    size=1024
)
```

This representation encodes local molecular substructures into a fixed-length fingerprint vector.

---

## Current Model

Model:

Random Forest Regressor

Current configuration:

```python
RandomForestRegressor(
    n_estimators=100,
    random_state=42,
    n_jobs=-1
)
```

Wrapped using:

```python
dc.models.SklearnModel
```

---

## Current Dataset Split

Initial workflow used a random split.

Typical split ratio:

- Train: 80%
- Validation: 10%
- Test: 10%

Important learning goal:

Compare random split with scaffold split.

Random split may place structurally similar molecules in both training and test sets.

Scaffold split is generally more challenging and can better test generalization to structurally different molecules.

---

## Current Results

Current approximate results:

```text
Train RMSE      ≈ 0.48
Validation RMSE ≈ 1.21
Test RMSE       ≈ 1.39
```

Current test metrics previously observed:

```text
Test MSE  ≈ 1.92
Test RMSE ≈ 1.39
```

Interpretation:

The training error is much lower than validation and test error.

This suggests that the current Random Forest model may be overfitting.

Do not conclude overfitting from one number alone. Always compare training, validation, and test performance.

---

## Data Exploration Findings

### Molecular Weight

Observed relationship:

Molecular weight has a negative correlation with logS.

Approximate Pearson correlation:

```text
r ≈ -0.64
```

Interpretation:

Higher molecular weight tends to be associated with lower aqueous solubility in this dataset.

However, molecular weight alone is not sufficient to predict solubility.

---

## Molecular Descriptors Explored

Descriptors discussed so far include:

- Molecular Weight
- LogP
- TPSA
- H-bond donors
- H-bond acceptors
- number of rings
- number of rotatable bonds

Important principle:

Do not interpret correlation as direct chemical causation.

---

## Important Technical Notes

### Array shape

Predictions may need to be flattened before evaluation:

```python
preds = preds.flatten()
y_true = y_true.flatten()
```

Always check:

```python
print(preds.shape)
print(y_true.shape)
```

---

### Reproducibility

Use a fixed seed when splitting the dataset or training stochastic models.

Example:

```python
seed=42
```

This makes model comparisons more meaningful.

---

## Current Learning Questions

Important questions to continue exploring:

1. Why is the Random Forest overfitting?
2. How much does performance change when using scaffold split?
3. How does a simple baseline compare with Random Forest?
4. Which molecular descriptors are most useful for solubility prediction?
5. How should model errors be interpreted chemically?
6. What molecules are predicted particularly poorly, and why?

---

## Next Planned Steps

1. Fix the random seed for dataset splitting.
2. Repeat the model using scaffold split.
3. Add a simple baseline model.
4. Compare train, validation, and test metrics.
5. Plot prediction error distributions.
6. Identify molecules with the largest prediction errors.
7. Interpret these errors using chemical descriptors.
8. Later extend the workflow toward drug developability prediction.

---

## Teaching Guidance for This Project

When using this project for learning:

- Prefer asking the learner to predict results before running code.
- Do not immediately provide complete code.
- Use progressive hints.
- Ask for scientific interpretation after every important result.
- Relate machine-learning behavior to molecular chemistry.
- Record recurring mistakes in `references/common-mistakes.md`.
- Generate a HANDOFF at the end of each learning session.
