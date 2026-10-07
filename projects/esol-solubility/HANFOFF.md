# AI4S Learning Handoff

## Project

DeepChem / ESOL learning — Working With Datasets

## What we are doing

We are using the uploaded *The DeepChem Book* as the conceptual learning path and cross-checking tutorial code against the official GitHub repository `deepchem/deepchem`.

The current learning goal is to build a solid foundation in DeepChem dataset handling before moving on to MoleculeNet, featurizers, splitters, and model training.

The teaching workflow used in this session is:

```text
Concept → Prediction → Code → Interpretation → Chemistry connection
```

## What has been completed

### 1. Core DeepChem Dataset structure

Learned that a Dataset is organized around:

```text
Dataset = X + y + w + ids
```

- `X`: model input features after featurization
- `y`: labels / prediction targets
- `w`: training weights
- `ids`: sample identifiers

For ESOL:
- `y` is measured logS.
- `ids` can be SMILES strings.

### 2. Correct interpretation of X

Clarified that `X` is not a fixed set of molecular descriptors.

```text
raw molecule / SMILES --featurizer--> X
```

Depending on the representation, `X` can be:
- molecular descriptors such as MW, TPSA, HBD, etc.
- a 1024-bit fingerprint
- graph-based molecular features
- another model-ready representation

### 3. NumpyDataset vs DiskDataset

Learned:

```text
NumpyDataset
→ data mainly stored in memory
→ convenient for small / medium datasets

DiskDataset
→ data stored on disk
→ useful for large datasets and DeepChem pipelines
```

Important clarification:
A dataset being returned as `DiskDataset` does not automatically mean it is too large for RAM. DeepChem loaders may use this format as part of their data pipeline.

### 4. Dataset access methods

Covered:

```python
dataset.X
dataset.y
dataset.w
dataset.ids
```

Direct access is simple, but for large datasets it may load a lot of data into memory.

Also learned:

```python
dataset.itersamples()
```

- iterates one sample at a time
- useful for inspection or debugging

and:

```python
dataset.iterbatches()
```

- processes multiple samples per batch
- more suitable for model training
- more computationally efficient

### 5. DataFrame conversion

Learned:

```python
dataset.to_dataframe()
```

This is useful for visually inspecting small datasets.

Important point:
If `X` has three feature columns, DeepChem may display them as `X1`, `X2`, and `X3`. These names do not imply fixed chemistry meanings; their interpretation depends on how `X` was constructed.

### 6. Shape interpretation

Established the rule:

```text
X.shape = (samples, features)
y.shape = (samples, tasks)
```

Examples:

```text
X.shape = (500, 1024)
y.shape = (500, 1)
```

means:
- 500 molecules
- 1024 input features per molecule
- 1 prediction target per molecule

For a 200-molecule, 4-descriptor, single-task example:

```text
X.shape   = (200, 4)
y.shape   = (200, 1)
w.shape   = (200, 1)
ids.shape = (200,)
```

### 7. ESOL-style NumpyDataset practice

Constructed a small dataset using three molecules:

```python
X = np.array([
    [46.07, 1, 0],
    [78.11, 0, 0],
    [60.05, 1, 0]
])

y = np.array([
    [-0.3],
    [-2.1],
    [0.2]
])

ids = np.array([
    "CCO",
    "c1ccccc1",
    "CC(=O)O"
])

dataset = dc.data.NumpyDataset(X=X, y=y, ids=ids)
```

Confirmed:
- `w` was automatically generated as ones.
- `ids` retained the supplied SMILES.
- `to_dataframe()` showed `X1`, `X2`, `X3`, `y`, `w`, and `ids`.

### 8. DiskDataset storage

Learned that:

```python
dc.data.DiskDataset.from_numpy(...)
```

can be used to convert NumPy arrays to a `DiskDataset`.

`data_dir` specifies the storage directory.

Also learned that:

```python
with tempfile.TemporaryDirectory() as data_dir:
```

creates temporary storage that is deleted after leaving the `with` block.

### 9. Batch, epoch, and random order

Learned:

```text
batch_size → samples processed per batch
epoch      → one complete pass through the dataset
shuffle    → change sample order between passes
```

In the tutorial context:

```python
deterministic=False
```

allows randomized sample order.

Also learned:

```text
number of batches = ceil(number of samples / batch_size)
```

Example:

```text
1000 samples
batch_size = 128
→ 8 batches per epoch
→ final batch has 104 samples
```

## Current status / where we are stuck

There is no blocking technical issue.

The main remaining learning gaps are conceptual:

- Need more practice separating **storage format** from **molecular representation**.
- Need to understand how DeepChem built-in loaders create and return train / validation / test datasets.
- Need to understand how `tasks`, `datasets`, and `transformers` work together in MoleculeNet.
- Need to see more examples of when `w` becomes important, especially in multitask datasets with missing labels.

## Common mistakes from this session

### 1. Treating X as a fixed set of descriptors

**Mistake**

Interpreted `X` as always meaning MW, H-bonds, rotatable bonds, TPSA, ring count, etc.

**Why it happened**

A specific descriptor representation was confused with the general meaning of model features.

**Fix**

Always ask how the molecule was featurized.

**Lesson**

`X` is the model-ready representation, not a fixed descriptor list.

---

### 2. Interpreting w as weighing percentage

**Mistake**

Initially interpreted `w` using chemistry terminology.

**Fix**

In DeepChem, `w` means training weights.

**Lesson**

Interpret symbols according to the library/API context, not chemistry notation.

---

### 3. Reversing NumPy shape dimensions

**Mistake**

Initially interpreted:

```text
X.shape = (10, 5)
```

as 10 columns and 5 rows.

**Fix**

Use:

```text
(samples, features)
```

for standard ML feature matrices.

**Lesson**

The first dimension is normally the number of samples.

---

### 4. Confusing batch size with number of batches

**Mistake**

For 1000 samples and `batch_size=128`, initially answered 128 batches.

**Fix**

```text
ceil(1000 / 128) = 8 batches
```

**Lesson**

`batch_size` means number of samples per batch, not number of batches.

---

### 5. Assuming DiskDataset always means “very large dataset”

**Mistake to avoid**

Do not infer dataset size only from the class name.

**Lesson**

DeepChem loaders may return `DiskDataset` for workflow and caching reasons even when a dataset could fit in RAM.

---

### 6. Assuming X1 / X2 / X3 have fixed chemistry meanings

**Mistake to avoid**

`to_dataframe()` may display feature columns generically.

**Lesson**

Feature meaning comes from how `X` was constructed, not from the automatically generated column names.

## Important decisions

- Continue using *The DeepChem Book* as the conceptual sequence.
- Continue checking code against the official `deepchem/deepchem` GitHub repository.
- Keep using ESOL examples because they connect directly to the user's molecular machine learning project.
- Keep the learning style interactive rather than giving conclusions immediately.

## Next steps

1. Start **An Introduction To MoleculeNet**.
2. Run and inspect:

```python
tasks, datasets, transformers = dc.molnet.load_delaney(...)
```

3. Identify what each returned object means.
4. Inspect the train / validation / test datasets using the Dataset concepts learned today.
5. Connect MoleculeNet loading to the existing ESOL workflow.
6. Then proceed to molecular fingerprints and splitters.

## Recommended starting point next session

Start with this question:

> When `dc.molnet.load_delaney(...)` returns `tasks, datasets, transformers`, what does each of the three objects represent?

Do not give the answer immediately. Continue with the same guided-learning format used today.
