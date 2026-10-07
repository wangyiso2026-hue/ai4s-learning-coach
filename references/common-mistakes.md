# Common Mistakes

This file records mistakes that have appeared during AI4S learning.

The goal is not only to fix each error once, but to recognize similar problems independently in the future.

---

## 1. Array Shape Mismatch

### What happened

Predictions and labels may have different shapes, for example:

```python
preds.shape
# (113, 1)

y_true.shape
# (113,)
```

This can cause problems when calculating metrics or plotting results.

### Fix

```python
preds = preds.flatten()
y_true = y_true.flatten()
```

### Lesson

Before calculating metrics, always check:

```python
print(preds.shape)
print(y_true.shape)
```

---

## 2. Random Split Without Fixed Seed

### What happened

Running the same notebook multiple times may give different train, validation, and test results.

### Cause

The dataset was randomly split without fixing the random seed.

### Fix

Use a fixed seed, for example:

```python
seed=42
```

### Lesson

Before comparing different models, make sure the data split is reproducible.

---

## 3. Confusing Correlation With Causation

### What happened

A molecular descriptor may correlate with solubility, but this does not mean that descriptor alone directly causes the observed solubility change.

### Example

Molecular weight may show a negative correlation with logS.

However, molecular weight is also related to other molecular properties such as:

- hydrophobicity
- molecular size
- aromaticity
- hydrogen bonding
- molecular flexibility

### Lesson

Use correlation to identify useful relationships, but use chemical reasoning to interpret them.

---

## 4. Judging a Model Only by Test RMSE

### What happened

A single test RMSE value was used to judge whether a model was good.

### Better approach

Compare:

- Train RMSE
- Validation RMSE
- Test RMSE

Example:

```text
Train RMSE = 0.48
Validation RMSE = 1.21
Test RMSE = 1.39
```

The much lower training error suggests possible overfitting.

### Lesson

Always evaluate both fitting ability and generalization ability.

---

## 5. Using Only One Molecular Feature

### What happened

A single feature such as molecular weight was used to explain solubility.

### Problem

Solubility depends on multiple molecular properties.

Useful descriptors may include:

- Molecular Weight
- LogP
- TPSA
- H-bond donors
- H-bond acceptors
- number of rings
- rotatable bonds

### Lesson

A single descriptor can reveal a trend, but usually cannot fully predict a complex molecular property.
