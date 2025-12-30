# API Documentation

This document provides detailed API documentation for the Python modules in this project.

## Table of Contents

- [Project Structure](#project-structure)
- [Core Modules](#core-modules)
  - [setup_project.py](#setup_projectpy)
  - [get-geo.py](#get-geopy)
  - [get-tcga.py](#get-tcgapy)
  - [feature_engineering.py](#feature_engineeringpy)
  - [model_training.py](#model_trainingpy)
  - [model_interpretation.py](#model_interpretationpy)
- [Usage Examples](#usage-examples)

---

## Project Structure

```
Brest-Cancer/
├── setup_project.py           # Project initialization
├── get-geo.py                 # GEO data download
├── get-tcga.py                # TCGA data download
├── verify_downloads.py        # Data verification
├── fix_GSE32646_download.py   # Fix specific dataset issues
├── check_preprocessed.py      # Result checking
├── feature_engineering.py     # Feature selection
├── model_training.py          # Model training
└── model_interpretation.py    # Model interpretation with SHAP
```

---

## Core Modules

### setup_project.py

**Purpose**: Initialize project directory structure.

#### Functions

##### `create_project_structure()`

Creates all necessary directories for the project.

```python
def create_project_structure():
    """Create the complete project directory structure"""
```

**Parameters**: None

**Returns**: None

**Creates**:
- `data/raw/GEO/` - Raw GEO datasets
- `data/raw/TCGA/` - Raw TCGA data
- `data/processed/` - Processed datasets
- `data/markers/` - Biomarker lists
- `src/` - Source code
- `models/` - Trained models
- `results/figures/` - Visualizations
- `results/tables/` - Summary tables
- `notebooks/` - Jupyter notebooks

**Example**:
```python
if __name__ == "__main__":
    create_project_structure()
```

---

### get-geo.py

**Purpose**: Download and preprocess Gene Expression Omnibus datasets.

#### Classes

##### `GEODataDownloader`

Main class for downloading GEO datasets.

```python
class GEODataDownloader:
    """Download and process GEO datasets for breast cancer analysis"""
    
    def __init__(self, output_dir='data/raw/GEO'):
        """
        Initialize the GEO data downloader.
        
        Parameters
        ----------
        output_dir : str, optional
            Directory to save downloaded data, by default 'data/raw/GEO'
        """
```

**Attributes**:
- `output_dir` (str): Output directory path
- `datasets` (dict): Dictionary of dataset information

**Methods**:

###### `download_dataset(gse_id, force_download=False)`

Download a single GEO dataset.

```python
def download_dataset(self, gse_id, force_download=False):
    """
    Download a single GEO dataset.
    
    Parameters
    ----------
    gse_id : str
        GEO Series ID (e.g., 'GSE25066')
    force_download : bool, optional
        If True, re-download even if file exists, by default False
        
    Returns
    -------
    gse : GEOparse.GEOTypes.GSE
        GEO Series object containing expression data and metadata
        
    Examples
    --------
    >>> downloader = GEODataDownloader()
    >>> gse = downloader.download_dataset('GSE25066')
    >>> print(f"Downloaded {len(gse.gsms)} samples")
    """
```

###### `extract_expression_matrix(gse, gse_id)`

Extract expression matrix from GEO Series object.

```python
def extract_expression_matrix(self, gse, gse_id):
    """
    Extract expression matrix from downloaded GEO dataset.
    
    Parameters
    ----------
    gse : GEOparse.GEOTypes.GSE
        GEO Series object
    gse_id : str
        GEO Series ID
        
    Returns
    -------
    expression_df : pd.DataFrame
        Expression matrix (genes × samples)
    metadata_df : pd.DataFrame
        Sample metadata with clinical annotations
    """
```

**Usage Example**:
```python
from get_geo import GEODataDownloader

# Initialize downloader
downloader = GEODataDownloader(output_dir='data/raw/GEO')

# Download single dataset
gse = downloader.download_dataset('GSE25066')

# Extract expression data
expression, metadata = downloader.extract_expression_matrix(gse, 'GSE25066')

print(f"Expression shape: {expression.shape}")
print(f"Samples: {len(metadata)}")
```

---

### get-tcga.py

**Purpose**: Download TCGA clinical data from UCSC Xena.

#### Classes

##### `TCGADataDownloader`

Main class for downloading TCGA data.

```python
class TCGADataDownloader:
    """Download TCGA clinical data"""
    
    def __init__(self, output_dir='data/raw/TCGA'):
        """
        Initialize TCGA data downloader.
        
        Parameters
        ----------
        output_dir : str, optional
            Directory to save data, by default 'data/raw/TCGA'
        """
```

**Methods**:

###### `download_clinical_data(cohort='BRCA')`

Download clinical data for specified cohort.

```python
def download_clinical_data(self, cohort='BRCA'):
    """
    Download TCGA clinical data.
    
    Parameters
    ----------
    cohort : str, optional
        TCGA cohort name, by default 'BRCA'
        
    Returns
    -------
    clinical_df : pd.DataFrame
        Clinical data with patient annotations
    """
```

---

### feature_engineering.py

**Purpose**: Feature selection and engineering for ML models.

#### Classes

##### `FeatureEngineer`

Main class for feature engineering and selection.

```python
class FeatureEngineer:
    """Feature engineering and selection for breast cancer prediction"""
    
    def __init__(self):
        """Initialize feature engineer."""
```

**Attributes**:
- `output_dir` (str): Directory for saving features
- `expression` (pd.DataFrame): Gene expression matrix
- `labels` (pd.Series): Response labels
- `metadata` (pd.DataFrame): Sample metadata

**Methods**:

###### `load_preprocessed_data()`

Load preprocessed expression data and labels.

```python
def load_preprocessed_data(self):
    """
    Load preprocessed expression and labels.
    
    Returns
    -------
    bool
        True if data loaded successfully
    """
```

###### `collect_prior_markers()`

Collect known breast cancer biomarkers from literature.

```python
def collect_prior_markers(self):
    """
    Collect known breast cancer marker genes.
    
    Returns
    -------
    markers : list
        List of known biomarker gene names
        
    Notes
    -----
    Based on established gene panels:
    - Oncotype DX (21 genes)
    - MammaPrint (70 genes)
    - PAM50 (50 genes)
    """
```

###### `differential_expression_analysis()`

Perform differential expression analysis between pCR and RD groups.

```python
def differential_expression_analysis(self):
    """
    Differential expression analysis (pCR vs RD).
    
    Returns
    -------
    de_results : pd.DataFrame
        DataFrame with columns:
        - gene: Gene name
        - fold_change: log2(pCR/RD)
        - p_value: t-test p-value
        - p_adj: FDR-adjusted p-value
        - significant: Boolean flag
    """
```

###### `select_features_rf(n_features=100)`

Select features using Random Forest RFE.

```python
def select_features_rf(self, n_features=100):
    """
    Select features using Random Forest Recursive Feature Elimination.
    
    Parameters
    ----------
    n_features : int, optional
        Number of features to select, by default 100
        
    Returns
    -------
    selected_genes : list
        List of selected gene names
    feature_importances : pd.DataFrame
        DataFrame with gene names and importance scores
        
    Examples
    --------
    >>> fe = FeatureEngineer()
    >>> fe.load_preprocessed_data()
    >>> genes, importances = fe.select_features_rf(n_features=50)
    >>> print(f"Selected {len(genes)} genes")
    """
```

**Complete Usage Example**:
```python
from feature_engineering import FeatureEngineer

# Initialize
fe = FeatureEngineer()

# Load data
fe.load_preprocessed_data()

# Get prior markers
markers = fe.collect_prior_markers()
print(f"Found {len(markers)} known markers")

# Differential expression
de_results = fe.differential_expression_analysis()
sig_genes = de_results[de_results['significant']]
print(f"Found {len(sig_genes)} DE genes")

# Feature selection
selected, importances = fe.select_features_rf(n_features=100)
print(f"Selected top {len(selected)} genes")

# Save results
importances.to_csv('data/processed/features/selected_genes_top100.csv', index=False)
```

---

### model_training.py

**Purpose**: Train and evaluate machine learning models.

#### Classes

##### `ModelTrainer`

Main class for model training and evaluation.

```python
class ModelTrainer:
    """Train and evaluate multiple ML models"""
    
    def __init__(self, n_features=100):
        """
        Initialize model trainer.
        
        Parameters
        ----------
        n_features : int, optional
            Number of features to use, by default 100
        """
```

**Attributes**:
- `n_features` (int): Number of features used
- `output_dir` (str): Directory for saving results
- `expression` (pd.DataFrame): Expression data
- `labels` (pd.Series): Response labels
- `selected_genes` (list): Selected gene names

**Methods**:

###### `load_data()`

Load expression data and selected features.

```python
def load_data(self):
    """
    Load preprocessed data and selected features.
    
    Returns
    -------
    bool
        True if data loaded successfully
    """
```

###### `prepare_data()`

Prepare feature matrix and labels for modeling.

```python
def prepare_data(self):
    """
    Prepare X and y for modeling.
    
    Returns
    -------
    X : pd.DataFrame
        Feature matrix (samples × genes)
    y : np.ndarray
        Binary labels (0=RD, 1=pCR)
    """
```

###### `define_models()`

Define all machine learning models to train.

```python
def define_models(self):
    """
    Define all ML models to train.
    
    Returns
    -------
    models : dict
        Dictionary mapping model names to sklearn estimators
        
    Notes
    -----
    Models included:
    - Random Forest
    - XGBoost
    - Gradient Boosting
    - AdaBoost
    - Logistic Regression
    - SVM
    """
```

###### `cross_validate_models(X, y, models)`

Perform 5-fold cross-validation for all models.

```python
def cross_validate_models(self, X, y, models):
    """
    Perform cross-validation for all models.
    
    Parameters
    ----------
    X : pd.DataFrame
        Feature matrix
    y : np.ndarray
        Labels
    models : dict
        Dictionary of models to evaluate
        
    Returns
    -------
    cv_results : pd.DataFrame
        Cross-validation results for each model
        
    Examples
    --------
    >>> trainer = ModelTrainer(n_features=100)
    >>> trainer.load_data()
    >>> X, y = trainer.prepare_data()
    >>> models = trainer.define_models()
    >>> results = trainer.cross_validate_models(X, y, models)
    >>> print(results)
    """
```

###### `bootstrap_validation(X, y, models, n_iterations=100)`

Perform bootstrap validation with confidence intervals.

```python
def bootstrap_validation(self, X, y, models, n_iterations=100):
    """
    Bootstrap validation with confidence intervals.
    
    Parameters
    ----------
    X : pd.DataFrame
        Feature matrix
    y : np.ndarray
        Labels
    models : dict
        Dictionary of models
    n_iterations : int, optional
        Number of bootstrap iterations, by default 100
        
    Returns
    -------
    bootstrap_results : pd.DataFrame
        Bootstrap results with confidence intervals
    """
```

**Usage Example**:
```python
from model_training import ModelTrainer

# Initialize trainer
trainer = ModelTrainer(n_features=100)

# Load data
trainer.load_data()

# Prepare features and labels
X, y = trainer.prepare_data()

# Define models
models = trainer.define_models()

# Cross-validation
cv_results = trainer.cross_validate_models(X, y, models)
print("\nCross-Validation Results:")
print(cv_results)

# Bootstrap validation
bootstrap_results = trainer.bootstrap_validation(X, y, models, n_iterations=100)
print("\nBootstrap Results (95% CI):")
print(bootstrap_results)

# Save results
cv_results.to_csv('results/models_top100/cv_results.csv', index=False)
```

---

### model_interpretation.py

**Purpose**: Interpret models using SHAP values.

#### Classes

##### `ModelInterpreter`

Main class for model interpretation with SHAP.

```python
class ModelInterpreter:
    """SHAP-based model interpretation"""
    
    def __init__(self, n_features=100):
        """
        Initialize model interpreter.
        
        Parameters
        ----------
        n_features : int, optional
            Number of features used, by default 100
        """
```

**Attributes**:
- `n_features` (int): Number of features
- `output_dir` (str): Output directory
- `expression` (pd.DataFrame): Expression data
- `labels` (pd.Series): Labels
- `selected_genes` (list): Gene names

**Methods**:

###### `load_data()`

Load data and selected features.

```python
def load_data(self):
    """
    Load preprocessed data and selected features.
    """
```

###### `prepare_data()`

Prepare data for modeling.

```python
def prepare_data(self):
    """
    Prepare data for modeling.
    
    Returns
    -------
    X : pd.DataFrame
        Feature matrix
    y : np.ndarray
        Labels
    """
```

###### `train_best_models(X, y)`

Train the best performing models for interpretation.

```python
def train_best_models(self, X, y):
    """
    Train the best performing models.
    
    Parameters
    ----------
    X : pd.DataFrame
        Feature matrix
    y : np.ndarray
        Labels
        
    Returns
    -------
    trained_models : dict
        Dictionary of trained models
    """
```

###### `calculate_shap_values(models, X)`

Calculate SHAP values for all models.

```python
def calculate_shap_values(self, models, X):
    """
    Calculate SHAP values for all models.
    
    Parameters
    ----------
    models : dict
        Trained models
    X : pd.DataFrame
        Feature matrix
        
    Returns
    -------
    shap_values_dict : dict
        SHAP values for each model
        
    Examples
    --------
    >>> interpreter = ModelInterpreter(n_features=100)
    >>> interpreter.load_data()
    >>> X, y = interpreter.prepare_data()
    >>> models = interpreter.train_best_models(X, y)
    >>> shap_values = interpreter.calculate_shap_values(models, X)
    >>> print(f"Calculated SHAP values for {len(shap_values)} models")
    """
```

###### `plot_shap_summary(shap_values, X, model_name)`

Create SHAP summary plots.

```python
def plot_shap_summary(self, shap_values, X, model_name):
    """
    Create SHAP summary plots.
    
    Parameters
    ----------
    shap_values : np.ndarray
        SHAP values
    X : pd.DataFrame
        Feature matrix
    model_name : str
        Name of the model
        
    Notes
    -----
    Saves figures to output_dir/figures/
    - shap_summary_{model_name}.png
    - shap_bar_{model_name}.png
    """
```

**Usage Example**:
```python
from model_interpretation import ModelInterpreter

# Initialize interpreter
interpreter = ModelInterpreter(n_features=100)

# Load data
interpreter.load_data()
X, y = interpreter.prepare_data()

# Train models
models = interpreter.train_best_models(X, y)

# Calculate SHAP values
shap_values = interpreter.calculate_shap_values(models, X)

# Create visualizations for each model
for model_name, shap_vals in shap_values.items():
    interpreter.plot_shap_summary(shap_vals, X, model_name)
    print(f"Created SHAP plots for {model_name}")

# Get feature importance ranking
mean_shap = np.abs(shap_values['XGBoost']).mean(axis=0)
importance_df = pd.DataFrame({
    'gene': X.columns,
    'importance': mean_shap
}).sort_values('importance', ascending=False)

print("\nTop 10 Most Important Genes:")
print(importance_df.head(10))
```

---

## Usage Examples

### Complete Pipeline Example

```python
"""
Example: Run complete ML pipeline programmatically
"""

# Step 1: Setup
from setup_project import create_project_structure
create_project_structure()

# Step 2: Download data
from get_geo import GEODataDownloader
downloader = GEODataDownloader()
for gse_id in ['GSE25066', 'GSE20271', 'GSE20194', 'GSE32646']:
    downloader.download_dataset(gse_id)

# Step 3: Feature engineering
from feature_engineering import FeatureEngineer
fe = FeatureEngineer()
fe.load_preprocessed_data()
selected_genes, importances = fe.select_features_rf(n_features=100)

# Step 4: Model training
from model_training import ModelTrainer
trainer = ModelTrainer(n_features=100)
trainer.load_data()
X, y = trainer.prepare_data()
models = trainer.define_models()
cv_results = trainer.cross_validate_models(X, y, models)

# Step 5: Model interpretation
from model_interpretation import ModelInterpreter
interpreter = ModelInterpreter(n_features=100)
interpreter.load_data()
X, y = interpreter.prepare_data()
models = interpreter.train_best_models(X, y)
shap_values = interpreter.calculate_shap_values(models, X)

print("✅ Pipeline complete!")
```

### Custom Feature Selection Example

```python
"""
Example: Custom feature selection with different criteria
"""
from feature_engineering import FeatureEngineer
import pandas as pd

fe = FeatureEngineer()
fe.load_preprocessed_data()

# Get differential expression results
de_results = fe.differential_expression_analysis()

# Custom filtering
custom_genes = de_results[
    (de_results['p_adj'] < 0.01) &  # Stricter p-value
    (abs(de_results['fold_change']) > 1.0)  # Larger fold change
]['gene'].tolist()

print(f"Selected {len(custom_genes)} genes with custom criteria")

# Save custom gene list
pd.DataFrame({'gene': custom_genes}).to_csv(
    'data/processed/features/custom_genes.csv',
    index=False
)
```

### Model Hyperparameter Tuning Example

```python
"""
Example: Hyperparameter tuning for XGBoost
"""
from model_training import ModelTrainer
from sklearn.model_selection import GridSearchCV
import xgboost as xgb

trainer = ModelTrainer(n_features=100)
trainer.load_data()
X, y = trainer.prepare_data()

# Define parameter grid
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [3, 5, 7],
    'learning_rate': [0.01, 0.1, 0.3],
}

# Grid search
xgb_model = xgb.XGBClassifier(random_state=42)
grid_search = GridSearchCV(
    xgb_model,
    param_grid,
    cv=5,
    scoring='roc_auc',
    n_jobs=-1,
    verbose=2
)

grid_search.fit(X, y)

print(f"Best parameters: {grid_search.best_params_}")
print(f"Best AUROC: {grid_search.best_score_:.3f}")
```

---

## Notes

### Random Seeds

For reproducibility, set random seeds at the start of your script:

```python
import random
import numpy as np
from sklearn.utils import check_random_state

# Set all random seeds
RANDOM_SEED = 42
random.seed(RANDOM_SEED)
np.random.seed(RANDOM_SEED)
```

### Memory Management

For large datasets:

```python
import gc

# After processing large objects
del large_object
gc.collect()
```

### Parallel Processing

Most scikit-learn and XGBoost models support parallel processing:

```python
model = RandomForestClassifier(n_jobs=-1)  # Use all CPU cores
```

---

## See Also

- [README.md](../README.md) - Project overview
- [PROJECT_WORKFLOW.md](../PROJECT_WORKFLOW.md) - Detailed workflow
- [CONTRIBUTING.md](../CONTRIBUTING.md) - Contribution guidelines

---

**For questions about the API, please open an issue on GitHub.**
