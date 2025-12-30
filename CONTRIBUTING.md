# Contributing to Breast Cancer Treatment Response Prediction

First off, thank you for considering contributing to this project! It's people like you that make this research tool valuable for the scientific community.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Testing Guidelines](#testing-guidelines)
- [Documentation Guidelines](#documentation-guidelines)

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code. Please report unacceptable behavior by opening an issue.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When you create a bug report, include as many details as possible:

- **Use a clear and descriptive title**
- **Describe the exact steps to reproduce the problem**
- **Provide specific examples** (code snippets, dataset information)
- **Describe the behavior you observed** and what you expected
- **Include error messages and stack traces**
- **Specify your environment** (OS, Python version, library versions)

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

- **Use a clear and descriptive title**
- **Provide a detailed description** of the suggested enhancement
- **Explain why this enhancement would be useful**
- **Include examples** of how it would be used
- **Reference relevant literature** if applicable

### Adding New Features

We welcome contributions that:

1. **Add new datasets**: Integration with additional GEO or TCGA datasets
2. **Improve models**: New ML algorithms or hyperparameter optimization
3. **Enhance preprocessing**: Better normalization or batch correction methods
4. **Add visualizations**: New plots or interpretability features
5. **Improve documentation**: Clarifications, examples, or tutorials
6. **Optimize performance**: Speed improvements or memory efficiency

## Development Setup

### 1. Fork and Clone

```bash
# Fork the repository on GitHub, then clone your fork
git clone https://github.com/YOUR-USERNAME/Brest-Cancer.git
cd Brest-Cancer
```

### 2. Create a Virtual Environment

```bash
# Create virtual environment
python -m venv venv

# Activate it
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
# Install all requirements
pip install -r requirements.txt

# Install development dependencies (if you add them)
pip install pytest flake8 black
```

### 4. Create a Branch

```bash
# Create a branch for your changes
git checkout -b feature/your-feature-name
# or
git checkout -b bugfix/issue-number-description
```

## Pull Request Process

### Before Submitting

1. **Update documentation** if you changed functionality
2. **Add tests** for new features when applicable
3. **Run existing tests** to ensure nothing broke
4. **Follow the coding standards** outlined below
5. **Update the CHANGELOG.md** with your changes

### Submitting the PR

1. **Push your branch** to your fork
   ```bash
   git push origin feature/your-feature-name
   ```

2. **Create a Pull Request** on GitHub with:
   - Clear title describing the change
   - Detailed description of what changed and why
   - Reference to related issues (e.g., "Fixes #123")
   - Screenshots for UI changes (if applicable)

3. **Wait for review**:
   - Address any feedback from reviewers
   - Make requested changes in new commits
   - Don't force-push after review has started

### PR Acceptance Criteria

Your PR will be merged if it:
- ✅ Follows the coding standards
- ✅ Includes appropriate tests
- ✅ Has clear documentation
- ✅ Passes all existing tests
- ✅ Has been reviewed and approved
- ✅ Doesn't introduce security vulnerabilities

## Coding Standards

### Python Style Guide

We follow PEP 8 with some modifications:

```python
# Good: Clear, descriptive names
def calculate_differential_expression(expression_matrix, labels):
    """
    Calculate differential expression between groups.
    
    Parameters
    ----------
    expression_matrix : pd.DataFrame
        Gene expression matrix (genes × samples)
    labels : pd.Series
        Sample labels for grouping
        
    Returns
    -------
    pd.DataFrame
        Differential expression results with p-values and fold changes
    """
    pass

# Bad: Unclear names, no documentation
def calc_de(em, l):
    pass
```

### Key Conventions

1. **Function Names**: Use `snake_case`
2. **Class Names**: Use `PascalCase`
3. **Constants**: Use `UPPER_CASE`
4. **Private Methods**: Prefix with `_underscore`
5. **Line Length**: Aim for 88 characters (Black formatter default)
6. **Docstrings**: Use NumPy style docstrings for all public functions

### Code Organization

```python
# Standard library imports
import os
import sys

# Third-party imports
import pandas as pd
import numpy as np
from sklearn.ensemble import RandomForestClassifier

# Local imports
from src.preprocessing import normalize_expression
```

### Error Handling

```python
# Good: Specific error handling with informative messages
try:
    expression = pd.read_csv(filepath)
except FileNotFoundError:
    print(f"❌ Error: Expression file not found at {filepath}")
    print("   Run 'python get-geo.py' to download datasets first")
    return None
except pd.errors.EmptyDataError:
    print(f"❌ Error: Expression file is empty at {filepath}")
    return None

# Bad: Catching all exceptions without context
try:
    expression = pd.read_csv(filepath)
except:
    pass
```

## Testing Guidelines

### Running Tests

```bash
# Run all tests
pytest

# Run specific test file
pytest tests/test_preprocessing.py

# Run with coverage
pytest --cov=src tests/
```

### Writing Tests

```python
import pytest
import pandas as pd
import numpy as np
from src.preprocessing import normalize_expression

def test_normalize_expression_shape():
    """Test that normalization preserves data shape"""
    # Arrange
    data = pd.DataFrame(np.random.rand(100, 20))
    
    # Act
    normalized = normalize_expression(data)
    
    # Assert
    assert normalized.shape == data.shape

def test_normalize_expression_range():
    """Test that normalized values are in expected range"""
    # Arrange
    data = pd.DataFrame(np.random.rand(100, 20))
    
    # Act
    normalized = normalize_expression(data)
    
    # Assert
    assert normalized.min().min() >= 0
    assert normalized.max().max() <= 1
```

## Documentation Guidelines

### Code Documentation

Every public function, class, and module should have a docstring:

```python
def select_features(expression, labels, n_features=100):
    """
    Select most informative features using Random Forest.
    
    This function performs feature selection using Random Forest-based
    Recursive Feature Elimination (RFE) to identify the most predictive
    genes for treatment response.
    
    Parameters
    ----------
    expression : pd.DataFrame
        Gene expression matrix with shape (n_genes, n_samples)
    labels : pd.Series or np.ndarray
        Binary labels (0=RD, 1=pCR) for each sample
    n_features : int, optional
        Number of features to select, by default 100
        
    Returns
    -------
    selected_genes : list
        List of selected gene names
    importances : pd.DataFrame
        Feature importance scores
        
    Examples
    --------
    >>> expr = pd.read_csv('expression.csv', index_col=0)
    >>> labels = pd.read_csv('labels.csv')['response']
    >>> genes, scores = select_features(expr, labels, n_features=50)
    >>> print(f"Selected {len(genes)} genes")
    
    Notes
    -----
    This method uses stratified cross-validation to ensure robust
    feature selection across different data splits.
    """
    pass
```

### README Updates

When adding new features, update the README.md:

1. Add to **Key Features** if it's a major feature
2. Update **Usage** section with examples
3. Add to **Dependencies** if you added new libraries
4. Update **Project Structure** if you added directories

### Changelog Updates

Always update CHANGELOG.md:

```markdown
## [Unreleased]

### Added
- New XGBoost hyperparameter tuning with GridSearchCV (#45)
- Support for additional GEO platforms (GPL570) (#47)

### Changed
- Improved batch effect correction using ComBat-seq (#46)
- Updated scikit-learn to version 1.3.0 (#48)

### Fixed
- Fixed memory leak in SHAP calculation (#49)
- Corrected p-value calculation in differential expression (#50)
```

## Scientific Integrity

### Research Standards

- **Reproducibility**: All results must be reproducible with set random seeds
- **Validation**: New methods should be validated on independent datasets
- **Citations**: Properly cite methods and datasets you use
- **Honesty**: Report both positive and negative results

### Data Ethics

- **Privacy**: Ensure patient data remains de-identified
- **Consent**: Only use publicly available datasets with proper permissions
- **Bias**: Be aware of and document potential biases in datasets

## Questions?

If you have questions about contributing, please:

1. Check the [existing documentation](README.md)
2. Search [existing issues](https://github.com/Aspect022/Brest-Cancer/issues)
3. Open a new issue with the "question" label

## Recognition

Contributors will be recognized in:
- The project README
- Release notes for significant contributions
- Academic publications if applicable

Thank you for contributing to advancing breast cancer research! 🎗️
