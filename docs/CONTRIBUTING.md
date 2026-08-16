# Contributing to SRE Intelligence Hub

Thank you for your interest in contributing! This guide outlines conventions and workflows for this repository.

## Table of Contents

- [Notebook Naming Conventions](#notebook-naming-conventions)
- [Notebook Template](#notebook-template)
- [Coding Standards](#coding-standards)
- [Reproducibility](#reproducibility)
- [Output Policy](#output-policy)
- [Submitting Changes](#submitting-changes)

---

## Notebook Naming Conventions

| Pattern | Example | Purpose |
|---------|---------|---------|
| `{topic}.ipynb` | `numpy.ipynb` | Main topic notebook |
| `{topic}-exercise.ipynb` | `classification-exercise.ipynb` | Exercise for learner |
| `{topic}-solution.ipynb` | `classification-solution.ipynb` | Solution to exercise |

**Rules:**
- Use lowercase with hyphens
- No spaces in filenames
- Group related notebooks in subdirectories

---

## Notebook Template

Each notebook should follow this structure:

```markdown
# Topic Title

## Objectives
- Learning objective 1
- Learning objective 2
- Learning objective 3

## Dataset
- Source: [where data comes from]
- Size: [approximate size]
- How to load: [code snippet]

## Expected Outcome
- What you'll produce (plot, model, metric)
- Expected runtime (CPU/GPU)

## Challenge (Optional)
- Stretch goal for advanced learners

---

## Code Section

```python
# imports
import numpy as np
import pandas as pd
```

---

## Notes

- Include markdown explanations between code cells
- Add comments in code for complex logic
- Reference common pitfalls in callout boxes
```

---

## Coding Standards

### Imports
```python
# Standard library imports first
import os
import sys

# Third-party imports
import numpy as np
import pandas as pd

# Local imports (if any)
# from .utils import helper
```

### Random Seeds
Always set seeds for reproducibility:
```python
import numpy as np
import random

# Set all seeds
np.random.seed(42)
random.seed(42)

# For sklearn
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# For tensorflow/keras
import tensorflow as tf
tf.random.set_seed(42)
```

### Plot Styling
```python
import matplotlib.pyplot as plt

# Use consistent style
plt.style.use('default')
plt.rcParams['figure.figsize'] = (8, 6)
plt.rcParams['font.size'] = 12
```

---

## Reproducibility

### Fast/Slow Mode
Use the shared `notebook_toggle.py` utility (repo root) instead of a one-off flag, so `NOTEBOOK_MODE=quick|full` behaves the same way in every notebook:

```python
import sys
from pathlib import Path

# notebook_toggle.py lives at the repo root; walk up from this notebook's own
# directory to find it, since Jupyter sets the kernel's cwd to the notebook's
# folder, not wherever `jupyter lab` was launched from.
try:
    repo_root = next(p for p in Path.cwd().resolve().parents if (p / "notebook_toggle.py").exists())
    sys.path.insert(0, str(repo_root))
    from notebook_toggle import get_mode
    mode = get_mode()
except (ImportError, StopIteration):
    # notebook_toggle.py isn't reachable (e.g. a Colab session that only
    # fetched this one file) — fall back to a sensible default.
    mode = "full"

N_SAMPLES = 100 if mode == "quick" else 10000
N_ITERATIONS = 10 if mode == "quick" else 100
```

Toggle it with `export NOTEBOOK_MODE=quick` before launching Jupyter. Note that `notebook_toggle.py`'s own default is `"quick"` when `NOTEBOOK_MODE` is unset — if your notebook's whole point is demonstrating scale (e.g. a distributed-processing demo), it's fine to invert that and default to `"full"` unless quick mode is explicitly requested; just say so in a comment.

### Synthetic Data
Use sklearn's `make_*` functions with `random_state=42`:
```python
from sklearn.datasets import make_classification, make_regression

# Classification
X, y = make_classification(n_samples=1000, n_features=20, random_state=42)

# Regression
X, y = make_regression(n_samples=1000, n_features=10, random_state=42)
```

---

## Output Policy

### Before Committing
**Commit notebooks with outputs rendered** — results should be readable on GitHub without anyone needing to run the notebook first.

1. In Jupyter Lab: `Kernel > Restart Kernel and Run All...` so outputs reflect the current code, top to bottom.
2. Or use nbconvert:
   ```bash
   jupyter nbconvert --to notebook --execute --inplace notebook.ipynb
   ```

### Why Keep Outputs?
- Results are readable on GitHub with no install or kernel required
- Reviewers see what a change actually produced, not just the code that produces it
- Forces the notebook to actually execute cleanly top to bottom before it's committed

---

## Submitting Changes

### Pull Request Guidelines

1. **Small, focused changes** - One topic per PR
2. **Test your notebook** - Restart the kernel and run all cells before submitting
3. **Keep outputs rendered** - Commit with fresh outputs from that full run, not stale or cleared ones
4. **Descriptive commits** - Explain what changed

### PR Title Format
- `feat: Add NumPy broadcasting challenges`
- `fix: Fix typo in pandas/README.md`
- `docs: Update classification notebook template`

---

## Getting Help

- Open an issue for questions
- Check existing issues before creating new ones
- Be respectful and constructive in discussions
