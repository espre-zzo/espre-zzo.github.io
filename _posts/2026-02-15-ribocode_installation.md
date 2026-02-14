---
title: "RiboCode installation error solving"
date: 2026-02-15 01:00:00 +0900
categories: [Bioinformatics, ribosome_profiling]
tags: [ribocode, ribosome-profiling, pyfasta]
---

[RiboCode](https://github.com/xryanglab/RiboCode) is a tool that used to analyze ribosome profiling data to find out ORF(Open Reading Frame). This post mainly covers the problem I encountered while installing RiboCode due to version mismatch (python2 legacy).

---

## 1. The Problem: Python 2 Legacy

RiboCode and its dependency **pyfasta** were originally written for Python 2. While RiboCode's `setup.py` claims Python 3 support, the actual source code still contains Python 2 syntax that breaks on modern Python (3.10+).

### 1.1. pyfasta Issues

`pyfasta==0.5.2` (last updated 2014) contains multiple Python 2-only patterns. Following box is what I found while using RiboCode.

| File | Error | Root Cause |
|------|-------|------------|
| `__init__.py` | `ModuleNotFoundError: No module named 'fasta'` | Absolute import (`from fasta import`) |
| `__init__.py` | `ModuleNotFoundError: No module named 'records'` | Absolute import (`from records import`) |
| `fasta.py` | `ImportError: cannot import name 'Mapping' from 'collections'` | `collections.Mapping` removed in Python 3.10 |
| `records.py` | Same `Mapping` error + `import cPickle` | `cPickle` merged into `pickle` in Python 3 |
| `records.py` | `NameError: name 'long' is not defined` | `long` type removed in Python 3 |
| `split_fasta.py` | `ImportError: No module named 'cStringIO'` | `cStringIO` merged into `io` in Python 3 |

### 1.2. RiboCode Issues

RiboCode's own `RiboCode/` package has similar problems:

| File | Error | Root Cause |
|------|-------|------------|
| `__init__.py` | `ModuleNotFoundError` | Absolute imports |
| `fasta.py` | `ModuleNotFoundError` | Absolute imports |
| `records.py` | `import cPickle` / `(int, long)` | Python 2 types |
| `split_fasta.py` | `from cStringIO import StringIO` | Python 2 module |
| `test_func.py` | `from minepy import MINE` (top-level) | Blocks import even when MIC is unused |

### 1.3. minepy Build Failure

RiboCode's `setup.py` lists `minepy` in `install_requires`. However:

- minepy has **no pre-built wheel** on PyPI (source-only `.tar.gz`)
- Its C extension build fails on Python 3.11+ due to `setuptools` incompatibility
- The project has been **unmaintained since 2020** ([minepy#39](https://github.com/minepy/minepy/issues/39))

When you run `pip install -e .` for RiboCode, pip automatically tries to build minepy from source, which fails with:

```text
ModuleNotFoundError: No module named 'pkg_resources'
ERROR: Failed to build 'minepy' when getting requirements to build wheel
```

> minepy is only used when `--dependence_test mic` is explicitly passed. The default mode (`none`) does not require it at all.
{: .prompt-info }

---

## 2. Solution: Step-by-Step Installation

### 2.1. Prerequisites

I have tested following method in macOS and Ubuntu-server.

- [Miniforge](https://github.com/conda-forge/miniforge) with `mamba`
- `git`

### 2.2. Step 1: Create conda environment

I have created `environment.yml` to install dependencies with mamba. `environment.yml` contains following:

```yaml
name: ribocode

channels:
  - conda-forge
  - bioconda
  - defaults

dependencies:
  - python=3.10
  - pip
  - git
  # C-extension packages (avoid pip build issues)
  - pysam
  - minepy
  - h5py
```

Then run:

```bash
mamba env create -f environment.yml
conda activate ribocode
```

> Install `pysam`, `minepy`, and `h5py` via conda is recommended because they contain C extensions. "conda-forge" provides pre-built binaries, avoiding compilation entirely. The conda-forge minepy binary is only available up to **Python 3.10** (last built March 2022).
{: .prompt-tip }

### 2.3. Step 2: Clone RiboCode

```bash
RIBOCODE_DIR=~/tools/RiboCode
git clone https://github.com/xryanglab/RiboCode.git "$RIBOCODE_DIR"
```

> I cloned RiboCode from GitHub instead of `pip install ribocode` because in need of patching the source files directly. Editable install (`pip install -e`) makes patches take effect immediately.
{: .prompt-info }

### 2.4. Step 3a: Patch setup.py

Remove `minepy` from `install_requires` so pip does not attempt to build it:

```bash
cd ~/tools/RiboCode
python -c "
from pathlib import Path
p = Path('setup.py')
t = p.read_text().replace(\"'minepy',\", '').replace(\"'minepy'\", '')
p.write_text(t)
print('Done: removed minepy from setup.py')
"
```

### 2.5. Step 3b: Install remaining dependencies

```bash
pip install "pyfasta==0.5.2" biopython numpy scipy statsmodels matplotlib HTSeq future
pip install --no-deps -e ~/tools/RiboCode
```

> `--no-deps` prevents pip from resolving `install_requires` again (minepy is already handled via conda).
{: .prompt-warning }

### 2.6. Step 3c: Patch pyfasta

```bash
python << 'PATCH_PYFASTA'
import importlib.util
from pathlib import Path

spec = importlib.util.find_spec("pyfasta")
if spec is None:
    raise SystemExit("[ERROR] pyfasta is not installed.")

pkg_dir = Path(spec.origin).resolve().parent
print(f"[INFO] pyfasta package directory: {pkg_dir}")

def patch_file(rel_path, replacements):
    path = pkg_dir / rel_path
    if not path.exists():
        print(f"[WARN] File not found: {path}")
        return
    text = path.read_text()
    orig = text
    for before, after in replacements:
        text = text.replace(before, after)
    if text != orig:
        path.write_text(text)
        print(f"[OK]   Patched: {rel_path}")
    else:
        print(f"[--]   No changes needed: {rel_path}")

patch_file("__init__.py", [
    ("from fasta import", "from .fasta import"),
    ("from records import", "from .records import"),
    ("from split_fasta import", "from .split_fasta import"),
])

patch_file("fasta.py", [
    ("from records import", "from .records import"),
    ("from collections import Mapping", "from collections.abc import Mapping"),
])

patch_file("records.py", [
    ("from collections import Mapping", "from collections.abc import Mapping"),
    ("import cPickle", "import pickle as cPickle"),
])

patch_file("split_fasta.py", [
    ("from cStringIO import StringIO", "from io import StringIO"),
])

# Add 'long = int' alias
records_path = pkg_dir / "records.py"
if records_path.exists():
    text = records_path.read_text()
    if "long = int" not in text:
        records_path.write_text(text + "\ntry:\n    long\nexcept NameError:\n    long = int\n\n")
        print("[OK]   Added 'long = int' alias: records.py")

print("[DONE] pyfasta patch complete.")
PATCH_PYFASTA
```

### 2.7. Step 3d: Patch RiboCode

```bash
RIBOCODE_DIR=~/tools/RiboCode

python << PATCH_RIBOCODE
from pathlib import Path

pkg_dir = Path("${RIBOCODE_DIR}") / "RiboCode"
print(f"[INFO] RiboCode package directory: {pkg_dir}")

def patch_file(path, replacements):
    if not path.exists():
        print(f"[WARN] File not found: {path}")
        return
    text = path.read_text()
    orig = text
    for before, after in replacements:
        text = text.replace(before, after)
    if text != orig:
        path.write_text(text)
        print(f"[OK]   Patched: {path.name}")
    else:
        print(f"[--]   No changes needed: {path.name}")

patch_file(pkg_dir / "__init__.py", [
    ("from fasta import ", "from .fasta import "),
    ("from records import ", "from .records import "),
    ("from split_fasta import ", "from .split_fasta import "),
])

patch_file(pkg_dir / "fasta.py", [
    ("from records import ", "from .records import "),
])

patch_file(pkg_dir / "records.py", [
    ("import cPickle", "import pickle as cPickle"),
    ("(int, long)", "int"),
])

patch_file(pkg_dir / "split_fasta.py", [
    ("from cStringIO import StringIO", "from io import StringIO"),
])

# Lazy import for minepy in test_func.py
test_func = pkg_dir / "test_func.py"
if test_func.exists():
    text = test_func.read_text()
    orig = text
    text = text.replace("from minepy import MINE\n", "")
    text = text.replace("from minepy import MINE", "")
    old = '    if method == "mic":\n        mine = MINE()'
    new = '    if method == "mic":\n        from minepy import MINE\n        mine = MINE()'
    text = text.replace(old, new)
    if text != orig:
        test_func.write_text(text)
        print("[OK]   Patched: test_func.py (minepy lazy import)")

print("[DONE] RiboCode patch complete.")
PATCH_RIBOCODE
```

### 2.8. Verify

```bash
RiboCode -h
```

If both commands print their help messages without errors, the installation is complete.

---

## 5. Summary of All Patches

### pyfasta 0.5.2

| File | Before (Python 2) | After (Python 3) |
|------|--------------------|-------------------|
| `__init__.py` | `from fasta import` | `from .fasta import` |
| `__init__.py` | `from records import` | `from .records import` |
| `__init__.py` | `from split_fasta import` | `from .split_fasta import` |
| `fasta.py` | `from records import` | `from .records import` |
| `fasta.py` | `from collections import Mapping` | `from collections.abc import Mapping` |
| `records.py` | `from collections import Mapping` | `from collections.abc import Mapping` |
| `records.py` | `import cPickle` | `import pickle as cPickle` |
| `records.py` | `long` type used | `long = int` alias added |
| `split_fasta.py` | `from cStringIO import StringIO` | `from io import StringIO` |

### RiboCode

| File | Before (Python 2) | After (Python 3) |
|------|--------------------|-------------------|
| `__init__.py` | `from fasta import` | `from .fasta import` |
| `fasta.py` | `from records import` | `from .records import` |
| `records.py` | `import cPickle` | `import pickle as cPickle` |
| `records.py` | `(int, long)` | `int` |
| `split_fasta.py` | `from cStringIO import StringIO` | `from io import StringIO` |
| `test_func.py` | Top-level `from minepy import MINE` | Lazy import inside `cal_dependence()` |
| `setup.py` | `'minepy'` in `install_requires` | Removed |

---

## References

- Xiao Z et al. (2018) *De novo annotation and characterization of the translatome with ribosome profiling data.* Nucleic Acids Research, 46(10):e61. [DOI: 10.1093/nar/gky179](https://doi.org/10.1093/nar/gky179)
- [RiboCode GitHub Repository](https://github.com/xryanglab/RiboCode)
- [minepy Issue #39: Python 3.11+ build failure](https://github.com/minepy/minepy/issues/39)
