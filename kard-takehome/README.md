# Kard Take-home: Merchant Classifier

## Setup

Requires Python 3.11.

### 1. Create a virtual environment

Windows (PowerShell):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

If PowerShell blocks the activation script, run this once and activate again:

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

macOS / Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Check the install

```bash
python -c "import pandas, sklearn, rapidfuzz; print('ok')"
```

### 4. Add the data

The data files are not included in this repo. Put them in `data/`:

```
data/
  transactions_sample.csv
  dev_labeled.csv
  test_unlabeled.csv
```
