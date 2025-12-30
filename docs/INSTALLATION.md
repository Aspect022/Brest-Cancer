# Installation Guide

This guide provides detailed installation instructions for the Breast Cancer Treatment Response Prediction project.

## Table of Contents

- [System Requirements](#system-requirements)
- [Installation Methods](#installation-methods)
- [Platform-Specific Instructions](#platform-specific-instructions)
- [Dependency Details](#dependency-details)
- [Verification](#verification)
- [Troubleshooting](#troubleshooting)
- [Advanced Configuration](#advanced-configuration)

---

## System Requirements

### Minimum Requirements

- **Operating System**: Windows 10+, macOS 10.14+, or Linux (Ubuntu 18.04+)
- **Python**: 3.8 or higher
- **RAM**: 8 GB minimum, 16 GB recommended
- **Disk Space**: 10 GB free space (for data and models)
- **Internet**: Required for downloading datasets

### Recommended Requirements

- **CPU**: 4+ cores for parallel processing
- **RAM**: 16 GB or more
- **Disk Space**: 20 GB+ SSD
- **GPU**: Optional, but accelerates some computations

### Software Dependencies

- Python 3.8+ with pip
- Git (for cloning repository)
- Optional: Jupyter Notebook (for interactive analysis)
- Optional: Docker (for containerized deployment)

---

## Installation Methods

### Method 1: Standard Installation (Recommended)

This method installs the project in a virtual environment.

#### Step 1: Install Python

**Check if Python is installed:**
```bash
python --version
# or
python3 --version
```

**If not installed:**

- **Windows**: Download from [python.org](https://www.python.org/downloads/)
- **macOS**: 
  ```bash
  brew install python@3.9
  ```
- **Linux (Ubuntu/Debian)**:
  ```bash
  sudo apt update
  sudo apt install python3.9 python3.9-venv python3-pip
  ```

#### Step 2: Clone the Repository

```bash
# Clone from GitHub
git clone https://github.com/Aspect022/Brest-Cancer.git

# Navigate to project directory
cd Brest-Cancer
```

#### Step 3: Create Virtual Environment

**Why virtual environment?**
- Isolates project dependencies
- Prevents conflicts with system packages
- Makes project reproducible

**Create and activate:**

```bash
# Create virtual environment
python -m venv venv

# Activate on Linux/macOS
source venv/bin/activate

# Activate on Windows
venv\Scripts\activate

# Your prompt should change to show (venv)
```

#### Step 4: Install Dependencies

```bash
# Upgrade pip
pip install --upgrade pip

# Install all requirements
pip install -r requirements.txt

# This will take 5-10 minutes
```

#### Step 5: Setup Project Structure

```bash
# Create necessary directories
python setup_project.py
```

#### Step 6: Verify Installation

```bash
# Test imports
python -c "import pandas, numpy, sklearn, xgboost, shap; print('✅ All packages installed!')"
```

---

### Method 2: Development Installation

For contributors who want to modify the code.

```bash
# Clone and enter directory
git clone https://github.com/Aspect022/Brest-Cancer.git
cd Brest-Cancer

# Create virtual environment
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows

# Install in editable mode
pip install -e .

# Install development dependencies
pip install pytest flake8 black pylint jupyter

# Setup pre-commit hooks (optional)
pip install pre-commit
pre-commit install
```

---

### Method 3: Conda Installation

For users who prefer Conda package manager.

```bash
# Create conda environment
conda create -n brest-cancer python=3.9

# Activate environment
conda activate brest-cancer

# Install dependencies
conda install pandas numpy scipy scikit-learn matplotlib seaborn
conda install -c conda-forge xgboost shap

# Install remaining packages with pip
pip install GEOparse lifelines scikit-survival pingouin
```

---

## Platform-Specific Instructions

### Windows

#### Additional Prerequisites

```powershell
# Install Visual C++ Build Tools (if needed)
# Download from: https://visualstudio.microsoft.com/visual-cpp-build-tools/

# Or install via chocolatey
choco install visualstudio2019buildtools
```

#### Common Issues

**Issue: "python" not recognized**
```powershell
# Add Python to PATH
# Control Panel → System → Advanced → Environment Variables
# Add: C:\Python39\ and C:\Python39\Scripts\
```

**Issue: Long path names**
```powershell
# Enable long paths in Windows
reg add HKLM\SYSTEM\CurrentControlSet\Control\FileSystem /v LongPathsEnabled /t REG_DWORD /d 1 /f
```

### macOS

#### Additional Prerequisites

```bash
# Install Homebrew (if not installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Python
brew install python@3.9

# Install additional tools
brew install git wget
```

#### Common Issues

**Issue: SSL certificate errors**
```bash
# Install certificates
/Applications/Python\ 3.9/Install\ Certificates.command
```

**Issue: Command line tools**
```bash
# Install Xcode command line tools
xcode-select --install
```

### Linux (Ubuntu/Debian)

#### Additional Prerequisites

```bash
# Update package list
sudo apt update

# Install Python and tools
sudo apt install python3.9 python3.9-venv python3-pip
sudo apt install git build-essential

# Install development headers (for compiling packages)
sudo apt install python3-dev libxml2-dev libxslt1-dev zlib1g-dev
```

#### Common Issues

**Issue: Permission denied**
```bash
# Don't use sudo with pip in virtual environment
# If needed for system-wide install:
sudo apt install python3-package-name
```

---

## Dependency Details

### Core Data Processing

```
pandas==2.0.3          # DataFrames and data manipulation
numpy==1.24.3          # Numerical computing
scipy==1.11.1          # Scientific computing and statistics
```

### Bioinformatics

```
GEOparse==2.0.3        # Download and parse GEO datasets
biopython==1.81        # Biological sequence analysis
scanpy==1.9.3          # Single-cell analysis (optional)
```

### Machine Learning

```
scikit-learn==1.2.2    # Core ML algorithms
xgboost==2.0.0         # Gradient boosting
imbalanced-learn==0.11.0  # SMOTE for class imbalance
```

### Interpretability

```
shap==0.43.0           # SHapley Additive exPlanations
```

### Visualization

```
matplotlib==3.7.2      # Basic plotting
seaborn==0.12.2        # Statistical visualization
plotly==5.16.1         # Interactive plots
```

### Statistical Analysis

```
statsmodels==0.14.0    # Statistical models
pingouin==0.5.3        # Statistical tests
```

### Optional Dependencies

```
jupyter==1.0.0         # Interactive notebooks
pytest==7.4.0          # Testing framework
black==23.7.0          # Code formatting
flake8==6.1.0          # Linting
```

---

## Verification

### Basic Verification

```bash
# Test Python version
python --version
# Should show: Python 3.8.x or higher

# Test package imports
python << EOF
import sys
print(f"Python: {sys.version}")

import pandas as pd
print(f"Pandas: {pd.__version__}")

import sklearn
print(f"Scikit-learn: {sklearn.__version__}")

import xgboost as xgb
print(f"XGBoost: {xgb.__version__}")

import shap
print(f"SHAP: {shap.__version__}")

print("\n✅ All core packages installed successfully!")
EOF
```

### Comprehensive Verification

```bash
# Create and run verification script
cat > verify_install.py << 'EOF'
#!/usr/bin/env python
"""Verify installation of all dependencies."""

import sys

# Required packages with versions
REQUIRED = {
    'pandas': '2.0.0',
    'numpy': '1.24.0',
    'scipy': '1.11.0',
    'GEOparse': '2.0.0',
    'sklearn': '1.2.0',
    'xgboost': '2.0.0',
    'imblearn': '0.11.0',
    'shap': '0.43.0',
    'matplotlib': '3.7.0',
    'seaborn': '0.12.0',
}

def check_version(pkg_name, min_version):
    """Check if package is installed with minimum version."""
    try:
        module = __import__(pkg_name)
        version = getattr(module, '__version__', 'unknown')
        
        # Simple version comparison
        if version >= min_version:
            print(f"✓ {pkg_name:15s} {version:10s} (>= {min_version})")
            return True
        else:
            print(f"✗ {pkg_name:15s} {version:10s} (< {min_version}) - UPDATE NEEDED")
            return False
    except ImportError:
        print(f"✗ {pkg_name:15s} NOT INSTALLED")
        return False

def main():
    """Run verification."""
    print(f"Python version: {sys.version}")
    print("\nChecking packages:\n")
    
    all_ok = True
    for pkg, min_ver in REQUIRED.items():
        if not check_version(pkg, min_ver):
            all_ok = False
    
    print("\n" + "="*50)
    if all_ok:
        print("✅ All packages installed correctly!")
        return 0
    else:
        print("❌ Some packages need attention")
        print("Run: pip install -r requirements.txt --upgrade")
        return 1

if __name__ == '__main__':
    sys.exit(main())
EOF

python verify_install.py
```

### Test Data Download

```bash
# Quick test of GEO download functionality
python -c "
import GEOparse
print('Testing GEO connection...')
try:
    # Test with small dataset
    gse = GEOparse.get_GEO(geo='GSE10', destdir='/tmp', silent=True)
    print('✅ GEO connection working!')
except Exception as e:
    print(f'❌ GEO connection failed: {e}')
"
```

---

## Troubleshooting

### Issue 1: pip install fails

**Error**: `ERROR: Could not build wheels for XXX`

**Solution**:
```bash
# Update pip and setuptools
pip install --upgrade pip setuptools wheel

# Install build dependencies
# On Linux:
sudo apt install python3-dev build-essential

# On macOS:
xcode-select --install

# Retry installation
pip install -r requirements.txt
```

### Issue 2: Memory errors during installation

**Error**: `MemoryError` or killed during install

**Solution**:
```bash
# Install packages one at a time
pip install pandas
pip install numpy
pip install scikit-learn
# ... etc

# Or increase swap space (Linux)
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### Issue 3: SSL certificate errors

**Error**: `SSL: CERTIFICATE_VERIFY_FAILED`

**Solution**:
```bash
# Option 1: Install certificates (macOS)
/Applications/Python\ 3.9/Install\ Certificates.command

# Option 2: Update certifi
pip install --upgrade certifi

# Option 3: Temporary workaround (not recommended for production)
pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org -r requirements.txt
```

### Issue 4: Conflicting dependencies

**Error**: `ERROR: Cannot install requirements due to conflicts`

**Solution**:
```bash
# Start fresh
deactivate  # if in virtual environment
rm -rf venv
python -m venv venv
source venv/bin/activate

# Install specific versions
pip install -r requirements.txt --no-cache-dir
```

### Issue 5: ImportError after installation

**Error**: `ImportError: No module named 'XXX'`

**Solution**:
```bash
# Verify you're in the virtual environment
which python
# Should show path to venv/bin/python

# Reinstall the specific package
pip install --force-reinstall XXX

# Check for typos in import statement
python -c "import XXX; print(XXX.__file__)"
```

---

## Advanced Configuration

### GPU Acceleration (Optional)

For faster XGBoost training:

```bash
# Install CUDA toolkit (NVIDIA GPUs only)
# Follow instructions at: https://developer.nvidia.com/cuda-downloads

# Install XGBoost with GPU support
pip uninstall xgboost
pip install xgboost --config-settings=build_cuda=1

# Verify GPU is detected
python -c "import xgboost as xgb; print(xgb.get_config())"
```

### Jupyter Notebook Setup

```bash
# Install Jupyter
pip install jupyter notebook ipykernel

# Add kernel to Jupyter
python -m ipykernel install --user --name=brest-cancer --display-name="Brest Cancer ML"

# Start Jupyter
jupyter notebook

# Create new notebook using "Brest Cancer ML" kernel
```

### Docker Installation (Alternative)

```dockerfile
# Create Dockerfile
cat > Dockerfile << 'EOF'
FROM python:3.9-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    git \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements
COPY requirements.txt .

# Install Python packages
RUN pip install --no-cache-dir -r requirements.txt

# Copy project files
COPY . .

# Run setup
RUN python setup_project.py

CMD ["/bin/bash"]
EOF

# Build image
docker build -t brest-cancer .

# Run container
docker run -it -v $(pwd)/data:/app/data brest-cancer
```

### Environment Variables

Create `.env` file (not committed to git):

```bash
# Create .env file
cat > .env << 'EOF'
# Data directories
DATA_DIR=/path/to/data
RESULTS_DIR=/path/to/results

# API keys (if needed)
# GEO_API_KEY=your_key_here

# Computational settings
N_JOBS=4
RANDOM_SEED=42
EOF

# Load in Python
python << 'EOF'
from dotenv import load_dotenv
import os

load_dotenv()
data_dir = os.getenv('DATA_DIR')
print(f"Data directory: {data_dir}")
EOF
```

### Performance Tuning

```bash
# Set environment variables for better performance
export OMP_NUM_THREADS=4
export OPENBLAS_NUM_THREADS=4
export MKL_NUM_THREADS=4

# Add to ~/.bashrc or ~/.zshrc for persistence
echo 'export OMP_NUM_THREADS=4' >> ~/.bashrc
```

---

## Next Steps

After successful installation:

1. **Read the Project Workflow**: See [PROJECT_WORKFLOW.md](../PROJECT_WORKFLOW.md)
2. **Run the Pipeline**: Start with `python setup_project.py`
3. **Download Data**: Run `python get-geo.py`
4. **Train Models**: Run `python model_training.py`

---

## Getting Help

If you encounter issues not covered here:

1. **Check existing issues**: [GitHub Issues](https://github.com/Aspect022/Brest-Cancer/issues)
2. **Search documentation**: [README.md](../README.md), [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
3. **Ask for help**: Open a new issue with:
   - Your OS and Python version
   - Complete error message
   - Steps to reproduce
   - Output of `pip list`

---

**Installation complete! Ready to start analyzing breast cancer data!** 🎗️
