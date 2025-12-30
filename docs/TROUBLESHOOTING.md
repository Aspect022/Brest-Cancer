# Troubleshooting Guide

This guide helps you diagnose and fix common issues when running the Breast Cancer Treatment Response Prediction pipeline.

## Table of Contents

- [Quick Diagnosis](#quick-diagnosis)
- [Installation Issues](#installation-issues)
- [Data Download Issues](#data-download-issues)
- [Processing Issues](#processing-issues)
- [Training Issues](#training-issues)
- [Memory Issues](#memory-issues)
- [Performance Issues](#performance-issues)
- [Results Issues](#results-issues)
- [Platform-Specific Issues](#platform-specific-issues)
- [Getting More Help](#getting-more-help)

---

## Quick Diagnosis

### Health Check Script

Run this to quickly identify issues:

```bash
python << 'EOF'
"""Quick health check for the project."""
import sys
import os

print("="*60)
print("SYSTEM HEALTH CHECK")
print("="*60)

# Python version
print(f"\n1. Python Version: {sys.version}")
if sys.version_info < (3, 8):
    print("   ❌ Python 3.8+ required")
else:
    print("   ✅ Python version OK")

# Check key packages
packages = ['pandas', 'numpy', 'sklearn', 'xgboost', 'shap']
print("\n2. Package Imports:")
for pkg in packages:
    try:
        __import__(pkg)
        print(f"   ✅ {pkg}")
    except ImportError:
        print(f"   ❌ {pkg} - NOT INSTALLED")

# Check directories
dirs = ['data', 'models', 'results']
print("\n3. Directory Structure:")
for d in dirs:
    if os.path.exists(d):
        print(f"   ✅ {d}/")
    else:
        print(f"   ❌ {d}/ - MISSING (run setup_project.py)")

# Check data files
print("\n4. Data Files:")
data_files = [
    'data/processed/combined/geo_expression_combined.csv',
    'data/processed/combined/geo_response_labels.csv'
]
for f in data_files:
    if os.path.exists(f):
        size = os.path.getsize(f) / (1024*1024)
        print(f"   ✅ {os.path.basename(f)} ({size:.1f} MB)")
    else:
        print(f"   ❌ {os.path.basename(f)} - MISSING")

print("\n" + "="*60)
EOF
```

---

## Installation Issues

### Issue: pip install fails with "Could not build wheels"

**Symptoms**:
```
ERROR: Could not build wheels for XXX, which is required to install pyproject.toml-based projects
```

**Solutions**:

**Option 1: Update build tools**
```bash
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt
```

**Option 2: Install system dependencies**

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install python3-dev build-essential libxml2-dev libxslt1-dev zlib1g-dev
pip install -r requirements.txt
```

**macOS:**
```bash
xcode-select --install
brew install python@3.9
pip install -r requirements.txt
```

**Windows:**
```powershell
# Install Visual C++ Build Tools
# Download from: https://visualstudio.microsoft.com/visual-cpp-build-tools/
# Then retry:
pip install -r requirements.txt
```

**Option 3: Install packages individually**
```bash
# Install one by one to identify problematic package
pip install pandas
pip install numpy
pip install scipy
pip install scikit-learn
pip install xgboost
pip install shap
# ... etc
```

---

### Issue: SSL Certificate Verification Failed

**Symptoms**:
```
SSL: CERTIFICATE_VERIFY_FAILED
urllib.error.URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED]>
```

**Solutions**:

**Option 1: Install certificates (macOS)**
```bash
/Applications/Python\ 3.9/Install\ Certificates.command
```

**Option 2: Update certifi**
```bash
pip install --upgrade certifi
```

**Option 3: Temporarily bypass (NOT recommended for production)**
```bash
pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org -r requirements.txt
```

---

### Issue: ImportError after installation

**Symptoms**:
```
ImportError: No module named 'XXX'
ModuleNotFoundError: No module named 'XXX'
```

**Solutions**:

**Check you're in virtual environment:**
```bash
which python
# Should show: /path/to/venv/bin/python

# If not, activate:
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate     # Windows
```

**Reinstall the package:**
```bash
pip install --force-reinstall XXX
```

**Check for typos:**
```python
# Common naming differences:
import sklearn  # not scikit-learn
import cv2      # not opencv-python
from imblearn import over_sampling  # not imbalanced-learn
```

---

## Data Download Issues

### Issue: GEO download fails or hangs

**Symptoms**:
```
📥 Downloading GSE25066...
[Hangs indefinitely or times out]
```

**Solutions**:

**Option 1: Check internet connection**
```bash
ping www.ncbi.nlm.nih.gov
curl -I https://ftp.ncbi.nlm.nih.gov/
```

**Option 2: Retry with force download**
```python
# Edit get-geo.py, change:
gse = downloader.download_dataset('GSE25066', force_download=True)
```

**Option 3: Use fix script for GSE32646**
```bash
python fix_GSE32646_download.py
```

**Option 4: Manual download**
```bash
# Download manually from GEO
# Visit: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE25066
# Click "Download family" → "SOFT formatted family file"
# Save to data/raw/GEO/

# Then load in Python:
import GEOparse
gse = GEOparse.get_GEO(filepath='data/raw/GEO/GSE25066_family.soft.gz')
```

**Option 5: Increase timeout**
```python
# Edit get-geo.py, add timeout parameter:
import socket
socket.setdefaulttimeout(300)  # 5 minutes
```

---

### Issue: Downloaded data is empty or corrupted

**Symptoms**:
```
❌ Error: Expression file is empty
ValueError: No objects to concatenate
```

**Solutions**:

**Verify downloads:**
```bash
python verify_downloads.py
```

**Check file sizes:**
```bash
ls -lh data/raw/GEO/
# Each .pkl file should be several MB
```

**Re-download specific dataset:**
```python
python -c "
from get_geo import GEODataDownloader
downloader = GEODataDownloader()
gse = downloader.download_dataset('GSE25066', force_download=True)
"
```

**Clear cache and retry:**
```bash
rm -rf data/raw/GEO/*.pkl
python get-geo.py
```

---

### Issue: "No response labels found"

**Symptoms**:
```
❌ Could not extract response labels
KeyError: 'response'
```

**Solutions**:

**Check metadata columns:**
```python
import pandas as pd
import pickle

# Load GSE
with open('data/raw/GEO/GSE25066.pkl', 'rb') as f:
    gse = pickle.load(f)

# Check available metadata
print("Available characteristics:")
for gsm_id, gsm in list(gse.gsms.items())[:1]:
    print(gsm.metadata['characteristics_ch1'])
```

**Common label names:**
- `pathologic_complete_response`
- `response`
- `pcr`
- `outcome`
- `residual_disease`

**Manually map labels:**
```python
# Edit get-geo.py to add manual mapping
def extract_response_label(metadata):
    """Custom function to extract response label"""
    # Add dataset-specific logic here
    pass
```

---

## Processing Issues

### Issue: Feature engineering fails

**Symptoms**:
```
ValueError: Not enough samples for feature selection
ValueError: All features have zero variance
```

**Solutions**:

**Check data loaded correctly:**
```python
import pandas as pd

expr = pd.read_csv('data/processed/combined/geo_expression_combined.csv', index_col=0)
labels = pd.read_csv('data/processed/combined/geo_response_labels.csv')

print(f"Expression shape: {expr.shape}")
print(f"Labels shape: {labels.shape}")
print(f"Label distribution:\n{labels['response'].value_counts()}")
```

**Reduce feature count:**
```bash
# Try with fewer features
python feature_engineering.py  # Change n_features to smaller value
```

**Check for missing values:**
```python
import pandas as pd
expr = pd.read_csv('data/processed/combined/geo_expression_combined.csv', index_col=0)

print(f"Missing values: {expr.isna().sum().sum()}")
print(f"Infinite values: {np.isinf(expr).sum().sum()}")

# Fill missing
expr = expr.fillna(expr.mean())
```

---

### Issue: Differential expression finds no genes

**Symptoms**:
```
⚠️  Found 0 differentially expressed genes
```

**Solutions**:

**Check p-value threshold:**
```python
# Edit feature_engineering.py
# Relax threshold:
significant = de_results[de_results['p_adj'] < 0.1]  # Instead of 0.05
```

**Check fold change threshold:**
```python
# Relax fold change:
significant = de_results[
    (de_results['p_adj'] < 0.05) &
    (abs(de_results['fold_change']) > 0.3)  # Instead of 0.5
]
```

**Check sample sizes:**
```python
labels = pd.read_csv('data/processed/combined/geo_response_labels.csv')
print(labels['response'].value_counts())
# Need at least 10 samples per group
```

---

## Training Issues

### Issue: Poor model performance (accuracy < 70%)

**Symptoms**:
```
Cross-Validation Accuracy: 0.65 ± 0.08
```

**Diagnostic Steps**:

**1. Check class balance:**
```python
import pandas as pd
labels = pd.read_csv('data/processed/combined/geo_response_labels.csv')
print(labels['response'].value_counts(normalize=True))

# Should be reasonably balanced after SMOTE
# If not, check SMOTE is being applied
```

**2. Check feature quality:**
```python
# Load selected features
import pandas as pd
genes = pd.read_csv('data/processed/features/selected_genes_top100.csv')
print(f"Selected {len(genes)} genes")

# Check if genes are informative
expr = pd.read_csv('data/processed/combined/geo_expression_combined.csv', index_col=0)
selected_expr = expr.loc[genes['gene']]
print(f"Expression variance: {selected_expr.var(axis=1).mean():.3f}")
# Should be > 0.1
```

**3. Try different feature counts:**
```bash
python model_training.py --n_features 50
python model_training.py --n_features 150
```

**4. Check for data leakage:**
```python
# Ensure SMOTE is only applied to training set
# Check model_training.py for proper cross-validation
```

**5. Tune hyperparameters:**
```python
# Edit model_training.py
# For XGBoost:
xgb.XGBClassifier(
    n_estimators=200,      # Increase
    max_depth=8,           # Adjust
    learning_rate=0.05,    # Decrease
    min_child_weight=3     # Adjust
)
```

---

### Issue: Model training is very slow

**Symptoms**:
- Training takes hours for single model
- No progress output

**Solutions**:

**1. Enable parallel processing:**
```python
# Edit model_training.py
RandomForestClassifier(n_jobs=-1)  # Use all cores
```

**2. Reduce cross-validation folds:**
```python
# Edit model_training.py
cv = StratifiedKFold(n_splits=3, shuffle=True)  # Instead of 5
```

**3. Reduce bootstrap iterations:**
```python
# Edit model_training.py
n_bootstrap = 50  # Instead of 100
```

**4. Use smaller feature set:**
```bash
python model_training.py --n_features 50
```

**5. Reduce dataset size for testing:**
```python
# For debugging only
X_sample = X.sample(n=100, random_state=42)
y_sample = y[X_sample.index]
```

---

### Issue: XGBoost crashes or gives warnings

**Symptoms**:
```
UserWarning: Use label_encoder instead of use_label_encoder
```

**Solutions**:

**Update parameter names:**
```python
# Old (deprecated):
xgb.XGBClassifier(use_label_encoder=False, eval_metric='logloss')

# New:
xgb.XGBClassifier(eval_metric='logloss')
```

**Check XGBoost version:**
```bash
pip list | grep xgboost
# Should be >= 2.0.0

# Update if needed:
pip install --upgrade xgboost
```

---

## Memory Issues

### Issue: MemoryError during training

**Symptoms**:
```
MemoryError: Unable to allocate X GB for an array
Killed
```

**Solutions**:

**1. Reduce feature count:**
```bash
python model_training.py --n_features 50
```

**2. Reduce batch size:**
```python
# For SHAP calculation, use smaller batches
X_sample = X.sample(n=100, random_state=42)
```

**3. Process models one at a time:**
```python
# Instead of training all models at once:
for name, model in models.items():
    # Train model
    # Save results
    # Clear memory
    del model
    gc.collect()
```

**4. Increase system swap:**
```bash
# Linux:
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Check:
free -h
```

**5. Close other applications:**
```bash
# Monitor memory usage:
# Linux:
htop
# macOS:
top
# Windows:
# Use Task Manager
```

---

### Issue: SHAP calculation runs out of memory

**Symptoms**:
```
MemoryError during shap.TreeExplainer
```

**Solutions**:

**1. Sample data:**
```python
# Edit model_interpretation.py
n_samples = 200  # Use subset
X_sample = X.sample(n=n_samples, random_state=42)
```

**2. Use TreeExplainer (faster):**
```python
# Instead of KernelExplainer:
explainer = shap.TreeExplainer(model)
```

**3. Process in batches:**
```python
batch_size = 50
all_shap_values = []
for i in range(0, len(X), batch_size):
    batch = X[i:i+batch_size]
    shap_values = explainer.shap_values(batch)
    all_shap_values.append(shap_values)
```

---

## Performance Issues

### Issue: Code runs but very slowly

**Symptoms**:
- Single operation takes minutes
- CPU usage low

**Solutions**:

**1. Enable parallelization:**
```python
import os
os.environ['OMP_NUM_THREADS'] = '4'
os.environ['OPENBLAS_NUM_THREADS'] = '4'
os.environ['MKL_NUM_THREADS'] = '4'
```

**2. Use optimized libraries:**
```bash
# Install Intel MKL (faster)
pip install mkl
```

**3. Profile code:**
```python
import cProfile
import pstats

profiler = cProfile.Profile()
profiler.enable()

# Your code here

profiler.disable()
stats = pstats.Stats(profiler)
stats.sort_stats('cumtime')
stats.print_stats(20)  # Top 20 slowest functions
```

**4. Check data types:**
```python
# Use appropriate dtypes
expr = expr.astype('float32')  # Instead of float64
```

---

## Results Issues

### Issue: Results files not generated

**Symptoms**:
- Empty results/ directory
- Missing figures

**Solutions**:

**Check for errors:**
```bash
# Run with verbose output
python model_training.py 2>&1 | tee training.log
```

**Check directory permissions:**
```bash
ls -la results/
chmod -R 755 results/
```

**Manually create directories:**
```bash
mkdir -p results/models_top100/figures
mkdir -p results/interpretation_top100/figures
```

---

### Issue: Figures are blank or corrupted

**Symptoms**:
- PNG files are 0 bytes
- Cannot open images

**Solutions**:

**Set non-interactive backend:**
```python
# Add at top of script
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
```

**Increase figure size:**
```python
plt.figure(figsize=(12, 8), dpi=150)
```

**Check save path:**
```python
import os
save_path = 'results/models_top100/figures/roc_curve.png'
os.makedirs(os.path.dirname(save_path), exist_ok=True)
plt.savefig(save_path, bbox_inches='tight')
plt.close()
```

---

## Platform-Specific Issues

### Windows Issues

**Issue: Path length errors**
```
FileNotFoundError: [Errno 2] No such file or directory
OSError: [Errno 22] Invalid argument
```

**Solution: Enable long paths**
```powershell
# Run as Administrator:
reg add HKLM\SYSTEM\CurrentControlSet\Control\FileSystem /v LongPathsEnabled /t REG_DWORD /d 1 /f

# Or use shorter paths:
cd C:\BC
git clone https://github.com/Aspect022/Brest-Cancer.git
```

**Issue: "python" not recognized**
```powershell
# Add to PATH:
# Control Panel → System → Advanced → Environment Variables
# Add: C:\Python39\ and C:\Python39\Scripts\
```

---

### macOS Issues

**Issue: "Permission denied" errors**
```bash
# Don't use sudo with pip in venv
# If global install needed:
pip install --user package_name
```

**Issue: xcrun error**
```bash
xcode-select --install
sudo xcode-select --reset
```

---

### Linux Issues

**Issue: "libgomp.so.1: cannot open"**
```bash
sudo apt install libgomp1
```

**Issue: Display errors (no GUI)**
```python
# Use non-interactive backend
import matplotlib
matplotlib.use('Agg')
```

---

## Getting More Help

### Before Asking for Help

1. **Search existing issues**: [GitHub Issues](https://github.com/Aspect022/Brest-Cancer/issues)
2. **Check documentation**: README.md, PROJECT_WORKFLOW.md
3. **Run health check** (see top of this guide)

### When Opening an Issue

Include:

```markdown
**Environment:**
- OS: [e.g., Ubuntu 20.04, macOS 12, Windows 10]
- Python version: [run `python --version`]
- Package versions: [run `pip list`]

**Problem:**
[Clear description]

**Steps to Reproduce:**
1. [First step]
2. [Second step]
3. [...]

**Expected Behavior:**
[What should happen]

**Actual Behavior:**
[What actually happens]

**Error Message:**
```
[Full error traceback]
```

**What I've Tried:**
- [Solution 1]
- [Solution 2]
```

### Useful Debug Commands

```bash
# System info
python --version
pip --version
uname -a  # Linux/macOS
systeminfo  # Windows

# Package versions
pip list
pip show pandas numpy scikit-learn xgboost

# File structure
tree -L 2  # Linux/macOS
dir /s  # Windows

# Memory usage
free -h  # Linux
vm_stat  # macOS
# Task Manager  # Windows

# Disk space
df -h  # Linux/macOS
dir  # Windows
```

---

## Emergency Recovery

### Start Fresh

If nothing works, start over:

```bash
# Backup any custom changes
cp -r /path/to/Brest-Cancer /path/to/Brest-Cancer.backup

# Remove everything
cd /path/to/Brest-Cancer
rm -rf venv data models results __pycache__

# Reinstall
python -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Setup
python setup_project.py
```

---

**Still stuck? Open an issue on GitHub with detailed information!** 🆘
