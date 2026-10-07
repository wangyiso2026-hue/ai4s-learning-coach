# AI4S Learning Handoff

## Project

DeepChem / ESOL learning — Working With Datasets

## Goal

Build a practical understanding of DeepChem Dataset objects before moving on to MoleculeNet and model training.

## What has been completed

- Learned the four core Dataset components: `X`, `y`, `w`, and `ids`.
- Distinguished molecular descriptors from generic model features: `X` is whatever representation is produced by the chosen featurization.
- Connected ESOL examples to DeepChem Dataset structure.
- Distinguished `NumpyDataset` from `DiskDataset`.
- Learned direct access, `itersamples()`, `iterbatches()`, and `to_dataframe()`.
- Practiced interpreting array shapes.
- Created a small ESOL-style `NumpyDataset` using three molecules and inspected `w`, `ids`, and `to_dataframe()`.
- Learned how `DiskDataset.from_numpy()` uses `data_dir` to store data on disk.
- Learned the roles of batch size, epoch, and shuffled/random sample order.

## Current results

Key working examples:

```text
Dataset = X + y + w + ids

X   = model input features
y   = labels / prediction targets
w   = training weights
ids = sample identifiers
```

For 500 molecules represented by a 1024-bit fingerprint and one logS target:

```text
X.shape = (500, 1024)
y.shape = (500, 1)
```

For 200 molecules with four descriptors and one task:

```text
X.shape   = (200, 4)
y.shape   = (200, 1)
w.shape   = (200, 1)
ids.shape = (200,)
```

Mini ESOL-style practice dataset:

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

Observed:
- `w` was automatically generated as ones.
- `ids` retained the supplied SMILES strings.
- `to_dataframe()` expanded the three feature columns as `X1`, `X2`, and `X3`.

## Current problems

- No blocking technical problem.
- Need continued practice distinguishing Dataset storage format from molecular representation.
- Need more familiarity with how DeepChem built-in loaders return `DiskDataset` even for relatively small datasets.

## Important decisions

- Use the uploaded DeepChem Book as the conceptual learning sequence.
- Cross-check tutorial code against the current official `deepchem/deepchem` GitHub repository.
- Use ESOL examples whenever possible to connect DeepChem abstractions to molecular machine learning.
- Continue the teaching flow: Concept → Prediction → Code → Interpretation → Chemistry connection.

## Common mistakes from this session

### Mistake 1

**What happened?**

Initially interpreted `X` as a fixed set of molecular properties such as MW, H-bonds, rotatable bonds, TPSA, and ring number.

**Cause**

Confused a particular descriptor representation with the general meaning of model features.

**Fix**

Use:

```text
SMILES --featurizer--> X
```

`X` depends on the chosen representation. It may contain molecular descriptors, a 1024-bit fingerprint, graph features, or another representation.

**Lesson**

Always ask: “How was this molecule featurized?” before interpreting `X`.

### Mistake 2

**What happened?**

Initially interpreted `w` as weighing percentage.

**Cause**

Mapped the symbol `w` to a chemistry meaning instead of the DeepChem Dataset definition.

**Fix**

In DeepChem, `w` means training weights. In simple single-task ESOL examples, valid labels commonly have weight 1.

**Lesson**

Interpret symbols from the library context rather than from chemistry notation.

### Mistake 3

**What happened?**

Initially reversed the dimensions of `X.shape = (10, 5)`.

**Cause**

Confused rows and columns in NumPy shape notation.

**Fix**

Read shape as:

```text
(samples, information per sample)
```

For `X`: `(samples, features)`.

For `y`: `(samples, tasks)`.

**Lesson**

The first dimension is normally the number of samples.

### Mistake 4

**What happened?**

For 1000 samples with `batch_size=128`, initially answered 128 batches per epoch.

**Cause**

Confused batch size with number of batches.

**Fix**

```text
number of batches = ceil(number of samples / batch_size)
ceil(1000 / 128) = 8
```

The final batch contains 104 samples.

**Lesson**

Batch size is samples per batch, not number of batches.

## Concepts learned

- `X`: model input features after featurization.
- `y`: labels or target values such as measured logS.
- `w`: training weights.
- `ids`: sample identifiers; in ESOL these can be SMILES strings.
- `NumpyDataset`: convenient for small/medium datasets that fit comfortably in RAM.
- `DiskDataset`: stores dataset data on disk and is useful for large datasets or DeepChem data pipelines.
- `dataset.X`, `dataset.y`, `dataset.w`, `dataset.ids`: direct access, but large arrays may consume a lot of RAM.
- `itersamples()`: iterate over one sample at a time.
- `iterbatches()`: iterate over mini-batches; more suitable for efficient model training.
- `to_dataframe()`: convenient for inspecting small datasets.
- `data_dir`: disk storage path for a `DiskDataset`.
- `TemporaryDirectory()`: temporary storage that is cleaned up after leaving the `with` block.
- `batch_size`: number of samples processed per batch.
- `epoch`: one complete pass through the dataset.
- `deterministic=False`: allows randomized sample order between iterations/epochs in the tutorial context.

## Questions still unclear

- How MoleculeNet loaders construct and return train/validation/test datasets internally.
- How featurizers such as fingerprints and graph representations change the exact structure of `X`.
- How Dataset weights become especially useful in multitask datasets with missing labels.

## Next steps

1. Start **An Introduction To MoleculeNet**.
2. Connect `dc.molnet.load_delaney()` outputs to the Dataset concepts learned today.
3. Inspect `tasks`, `datasets`, and `transformers` for ESOL.
4. Continue comparing the book tutorial with the current official DeepChem GitHub implementation.

## Recommended starting point for next session

Run `dc.molnet.load_delaney(...)` and identify what is returned as `tasks`, `datasets`, and `transformers` before discussing MoleculeNet in detail.
