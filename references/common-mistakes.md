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

---

## 6. Treating X as a Fixed Set of Molecular Descriptors

### What happened

`X` was initially interpreted as always meaning molecular properties such as MW, H-bonds, rotatable bonds, TPSA, and ring count.

### Cause

A specific descriptor representation was confused with the general Dataset definition of features.

### Fix

Use the general relationship:

```text
raw molecule / SMILES --featurizer--> X
```

Depending on the featurizer, `X` may contain descriptors, a fingerprint vector, graph features, or another molecular representation.

### Lesson

Before interpreting `X`, identify the featurizer or feature-generation method.

---

## 7. Confusing Dataset w With Chemical Weight or Weight Percentage

### What happened

`w` in a DeepChem Dataset was interpreted as weighing percentage.

### Cause

The symbol was interpreted using chemistry notation rather than DeepChem terminology.

### Fix

In DeepChem, `w` stores training weights. In a simple single-task dataset, valid labels commonly have weight 1.

### Lesson

Interpret variable names according to the API/library context before mapping them to chemistry meanings.

---

## 8. Reversing NumPy Shape Dimensions

### What happened

`X.shape = (10, 5)` was initially interpreted as 10 columns and 5 rows.

### Cause

The order of NumPy shape dimensions was reversed.

### Fix

For standard 2D ML arrays:

```text
X.shape = (samples, features)
y.shape = (samples, tasks)
```

For example:

```text
X.shape = (500, 1024)
y.shape = (500, 1)
```

means 500 molecules, 1024 features per molecule, and one prediction target.

### Lesson

Read the first dimension as the number of samples unless the data structure explicitly says otherwise.

---

## 9. Confusing Batch Size With Number of Batches

### What happened

For 1000 samples with `batch_size=128`, the number of batches was initially identified as 128.

### Cause

`batch_size` was confused with the number of batches in one epoch.

### Fix

Use:

```text
number of batches = ceil(number of samples / batch_size)
```

For 1000 samples:

```text
ceil(1000 / 128) = 8 batches
```

The first 7 batches contain 128 samples and the final batch contains 104.

### Lesson

`batch_size` means samples per batch. The number of batches depends on both dataset size and batch size.
