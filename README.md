# Breast Cancer Treatment Response Prediction

A comprehensive machine learning pipeline for predicting pathological complete response (pCR) to neoadjuvant chemotherapy in breast cancer patients using gene expression data.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

## 📚 Documentation

- **[Installation Guide](docs/INSTALLATION.md)** - Detailed setup instructions
- **[Project Workflow](PROJECT_WORKFLOW.md)** - Step-by-step explanation of the entire pipeline
- **[API Documentation](docs/API.md)** - Module and function reference
- **[Troubleshooting](docs/TROUBLESHOOTING.md)** - Common issues and solutions
- **[Contributing Guidelines](CONTRIBUTING.md)** - How to contribute to this project
- **[Code of Conduct](CODE_OF_CONDUCT.md)** - Community standards
- **[Security Policy](SECURITY.md)** - Security guidelines and reporting
- **[Changelog](CHANGELOG.md)** - Version history and updates

## 🎯 Overview

This project implements a complete end-to-end machine learning workflow for breast cancer treatment response prediction. It integrates multiple public gene expression datasets (GEO) and clinical data (TCGA) to identify molecular biomarkers that can predict treatment outcomes in breast cancer patients, particularly those with triple-negative breast cancer (TNBC).

### Key Features

- **Multi-dataset Integration**: Downloads and processes data from 4 GEO datasets (GSE25066, GSE20271, GSE20194, GSE32646) and TCGA-BRCA
- **Comprehensive Preprocessing**: Automated data cleaning, normalization, and batch effect correction
- **Feature Engineering**: Differential expression analysis and Random Forest-based feature selection
- **Multiple ML Models**: Trains and evaluates 6 different classifiers (Random Forest, XGBoost, Gradient Boosting, AdaBoost, Logistic Regression, SVM)
- **Model Interpretation**: SHAP-based interpretability for identifying key predictive biomarkers
- **Robust Evaluation**: 5-fold cross-validation with bootstrap confidence intervals and comprehensive performance metrics

## 📊 Dataset Information

### GEO Datasets (Gene Expression Omnibus)
- **GSE25066**: Development dataset - 170 TNBC patients (57 pCR, 113 RD)
- **GSE20271**: Validation dataset 1 - 58 TNBC patients (13 pCR, 45 RD)
- **GSE20194**: Validation dataset 2 - 71 TNBC patients (25 pCR, 46 RD)
- **GSE32646**: Validation dataset 3 - 26 TNBC patients (10 pCR, 16 RD)

**Total**: 325 samples across 4 independent cohorts  
**Platform**: Affymetrix HG U133A / U133 Plus 2.0

### TCGA Dataset
- **TCGA-BRCA**: ~1,198 breast cancer patients with comprehensive clinical data
- **Purpose**: Additional validation and external clinical feature analysis

## 🚀 Quick Start

### Prerequisites
- Python 3.8 or higher
- 8 GB RAM minimum (16 GB recommended)
- 10 GB free disk space

### Installation

```bash
# Clone the repository
git clone https://github.com/Aspect022/Brest-Cancer.git
cd Brest-Cancer

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Create project structure
python setup_project.py
```

**For detailed installation instructions, see [Installation Guide](docs/INSTALLATION.md)**

## 📖 Usage

### Complete Workflow

Run the entire pipeline in sequence:

```bash
# Step 1: Setup project directories
python setup_project.py

# Step 2: Download GEO gene expression datasets
python get-geo.py

# Step 3: Download TCGA clinical data
python get-tcga.py

# Step 4: Verify downloads
python verify_downloads.py

# Step 5: Feature engineering and selection
python feature_engineering.py

# Step 6: Train and evaluate models
python model_training.py

# Step 7: Interpret models with SHAP
python model_interpretation.py

# Step 8: Check results
python check_preprocessed.py
```

### Individual Steps

**Download Data**
```bash
python get-geo.py        # Download GEO datasets
python get-tcga.py       # Download TCGA clinical data
```

**Train Models with Different Feature Counts**
```bash
python model_training.py --n_features 50
python model_training.py --n_features 100
python model_training.py --n_features 150
```

**Model Interpretation**
```bash
python model_interpretation.py --n_features 100
```

## 📁 Project Structure

```
Brest-Cancer/
├── README.md                    # This file
├── requirements.txt             # Python dependencies
├── setup_project.py            # Create project directories
│
├── get-geo.py                  # Download GEO datasets
├── get-tcga.py                 # Download TCGA data
├── fix_GSE32646_download.py    # Fix specific dataset issues
├── verify_downloads.py         # Verify data integrity
│
├── feature_engineering.py      # Feature selection pipeline
├── model_training.py           # Model training and evaluation
├── model_interpretation.py     # SHAP-based interpretation
├── check_preprocessed.py       # Check processed data
│
├── data/                       # Data directory (created automatically)
│   ├── raw/                    # Raw downloaded data
│   │   ├── GEO/               # GEO datasets
│   │   └── TCGA/              # TCGA clinical data
│   ├── processed/             # Preprocessed data
│   └── markers/               # Known biomarker lists
│
├── models/                     # Trained models (auto-generated)
│
├── results/                    # Results and figures
│   ├── models_top50/          # Results with 50 features
│   ├── models_top100/         # Results with 100 features
│   ├── models_top150/         # Results with 150 features
│   ├── interpretation_top50/  # SHAP analysis (50 features)
│   ├── interpretation_top100/ # SHAP analysis (100 features)
│   └── interpretation_top150/ # SHAP analysis (150 features)
│
└── notebooks/                  # Jupyter notebooks (optional)
```

## 🔬 Methodology

### 1. Data Acquisition
- Downloads gene expression data from GEO database
- Retrieves clinical annotations including treatment response (pCR/RD)
- Collects TCGA-BRCA clinical data for additional validation

### 2. Preprocessing
- Log2 transformation of expression values
- Quantile normalization across samples
- Batch effect correction using ComBat
- Probe-to-gene mapping and duplicate handling

### 3. Feature Selection
- Collection of known breast cancer biomarkers
- Differential expression analysis (pCR vs RD)
- Random Forest Recursive Feature Elimination (RFE)
- Top 50/100/150 gene signatures extracted

### 4. Model Training
- **Algorithms**: Random Forest, XGBoost, Gradient Boosting, AdaBoost, Logistic Regression, SVM
- **Class Imbalance**: SMOTE (Synthetic Minority Over-sampling Technique)
- **Validation**: 5-fold stratified cross-validation
- **Metrics**: Accuracy, AUROC, Precision, Recall, F1-score, MCC

### 5. Model Evaluation
- Cross-validation performance assessment
- Bootstrap confidence intervals (100 iterations)
- ROC curves and precision-recall curves
- Confusion matrices and classification reports

### 6. Model Interpretation
- SHAP (SHapley Additive exPlanations) values
- Feature importance ranking
- Waterfall plots and summary plots
- Biomarker identification

## 📈 Results Summary

### Performance (Top 100 Genes)

**Cross-Validation (5-fold):**
- **Best Model**: XGBoost
  - Accuracy: 86.10% ± 2.35%
  - AUROC: 88.84% ± 3.89%
  - F1-score: 64.19% ± 7.00%

**Bootstrap Validation (100 iterations):**
- **SVM** achieved highest accuracy: 91.28% [87.31%, 94.36%]
- **XGBoost** achieved highest recall: 83.17% [71.43%, 92.06%]
- **Gradient Boosting** showed best precision: 79.90% [69.67%, 88.89%]

### Top Predictive Genes

The model identified several key genes associated with treatment response:
1. **204822_at** (importance: 0.294)
2. **209686_at** (importance: 0.222)
3. **204767_s_at** (importance: 0.180)
4. **201584_s_at** (importance: 0.159)
5. **219051_x_at** (importance: 0.158)

*See `results/interpretation_top100/interpretation_report.txt` for complete list*

## 🛠️ Dependencies

### Core Libraries
- **Data Processing**: pandas, numpy, scipy
- **Bioinformatics**: GEOparse, biopython, scanpy
- **Machine Learning**: scikit-learn, xgboost, imbalanced-learn
- **Interpretability**: shap
- **Survival Analysis**: lifelines, scikit-survival
- **Visualization**: matplotlib, seaborn, plotly
- **Statistics**: statsmodels, pingouin

See `requirements.txt` for complete list with versions.

## 🔍 Troubleshooting

Having issues? Check our comprehensive troubleshooting guide:

**[📖 Troubleshooting Guide](docs/TROUBLESHOOTING.md)**

### Quick Fixes

**Dataset download fails:**
```bash
python get-geo.py  # Retry download
python fix_GSE32646_download.py  # Fix specific dataset
```

**Memory errors:**
```bash
python model_training.py --n_features 50  # Use fewer features
```

**Poor performance:**
- Check class balance in labels
- Try different feature counts (50, 100, 150)
- Verify SMOTE is applied

For detailed solutions, see the [Troubleshooting Guide](docs/TROUBLESHOOTING.md).

## 🤝 Contributing

We welcome contributions! Please read our [Contributing Guidelines](CONTRIBUTING.md) to get started.

### Ways to Contribute

- 🐛 Report bugs or issues
- 💡 Suggest new features
- 📖 Improve documentation
- 🔬 Add new datasets or models
- 🧪 Write tests

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

**Research Use Disclaimer**: This software is intended for research and educational purposes only. It is NOT intended for clinical use or medical decision-making. See [LICENSE](LICENSE) for full terms.

## 🙏 Acknowledgments

- **[GEO Database](https://www.ncbi.nlm.nih.gov/geo/)**: For providing public gene expression datasets
- **[TCGA Project](https://www.cancer.gov/tcga)**: For comprehensive cancer genomics data
- **[UCSC Xena](https://xenabrowser.net/)**: For easy access to TCGA clinical data

## 📚 Citation

If you use this code in your research, please cite:

```bibtex
@software{breast_cancer_prediction,
  title={Breast Cancer Treatment Response Prediction},
  author={Aspect022},
  year={2024},
  version={1.0.0},
  url={https://github.com/Aspect022/Brest-Cancer}
}
```

## 📧 Contact & Support

- **Issues**: [GitHub Issues](https://github.com/Aspect022/Brest-Cancer/issues)
- **Discussions**: [GitHub Discussions](https://github.com/Aspect022/Brest-Cancer/discussions)
- **Documentation**: See [docs/](docs/) folder

For questions, issues, or collaborations, please open an issue on GitHub.

## ⭐ Show Your Support

If you find this project useful, please consider:
- ⭐ Starring the repository
- 📢 Sharing with colleagues
- 🐛 Reporting bugs
- 💡 Suggesting improvements

## 📜 Related Resources

### Research Papers
- Original dataset publications in GEO
- SHAP interpretability: [Lundberg & Lee (2017)](https://arxiv.org/abs/1705.07874)
- SMOTE: [Chawla et al. (2002)](https://arxiv.org/abs/1106.1813)

### Similar Projects
- [scikit-survival](https://github.com/sebp/scikit-survival) - Survival analysis
- [PyDESeq2](https://github.com/owkin/PyDESeq2) - Differential expression

### Learning Resources
- [SHAP Documentation](https://shap.readthedocs.io/)
- [XGBoost Documentation](https://xgboost.readthedocs.io/)
- [GEOparse Documentation](https://geoparse.readthedocs.io/)

---

**⚠️ Important Note**: This is a research tool and should not be used for clinical decision-making without proper validation and regulatory approval.
