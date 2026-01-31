# PPI Hotspot Pipeline

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)

> Hierarchical feature selection pipeline for protein-protein interaction (PPI) hotspot prediction using structural descriptors from STING_RDB.

## Citation

**If you use this code or data, please cite our paper:**
```bibtex
@article{verna2026hotspot,
  title={A Hierarchical Pipeline for Protein-Protein Interaction Hotspot Prediction Using Structural Descriptors from STING\_RDB},
  author={Verna, Rodrigo Perez and Yano, In{\'a}cio Henrique and Mazoni, Ivan and Arni, Raghuvir Krishnaswamy and Neshich, Goran and Gomes, Vit{\'o}ria Zanon and Zafalon, Geraldo Francisco Doneg{\'a}},
 
}
```

##  Overview

This repository contains the complete implementation of a hierarchical feature selection pipeline for predicting protein-protein interaction hotspots. The methodology achieves state-of-the-art performance through:

- **97.6% dimensional reduction**: 2,412 → 58 descriptors
- **Rigorous validation**: GroupKFold with homology control
- **Best performance**: AUC-ROC = 0.821, MCC = 0.538, F1 = 0.715
- **Explainability**: SHAP analysis revealing biological relevance

### Key Features

- Complete hierarchical pipeline (mRMR → KS test → VIF → Boruta)
- Three ML models: Random Forest, SVM-RBF, XGBoost  
- Processed dataset (918 residues, 58 features)  
- Trained models ready for prediction  
- SHAP analysis for interpretability  
- Reproduction notebooks  

---

##  Quick Start

### Installation
```bash
# Clone repository
git clone https://github.com/rodrigo-verna/ppi-hotspot-pipeline.git
cd ppi-hotspot-pipeline

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Basic Usage
```python
from src.modeling import HotspotPredictor

# Load trained model
predictor = HotspotPredictor.load('results/models/svm_gridsearch.pkl')

# Predict hotspots for new protein
predictions = predictor.predict(protein_features)
```

### Reproduce Paper Results
```bash
# Run complete pipeline
python scripts/run_full_pipeline.py

```

---

##  Results Summary

| Pipeline | Model | AUC-ROC | MCC | F1-Score |
|----------|-------|---------|-----|----------|
| GroupKFold | Random Forest | 0.818 | 0.520 | 0.702 |
| GridSearchCV | SVM-RBF | **0.821** | 0.478 | 0.688 |
| GridSearchCV | XGBoost | 0.806 | **0.538** | **0.715** |

**Comparison with baseline (5 classical descriptors):**
- AUC-ROC: +50.9% (0.544 → 0.821)
- MCC: +376% (0.113 → 0.538)

---

---

##  Methodology

### Hierarchical Feature Selection Pipeline

1. **Initial Cleaning** (2,412 → 1,455)
   - Remove evolutionary conservation descriptors
   - Remove zero-variance and redundant features
   
2. **Domain Filters** (1,455 → 567)
   - ID-like criterion (nunique/N > 0.9)
   - Intra-protein constancy filter
   
3. **In-Fold mRMR** (567 → 494)
   - Minimum Redundancy Maximum Relevance
   - GroupKFold cross-validation
   
4. **Statistical Validation** (494 → 276)
   - Kolmogorov-Smirnov test
   - FDR correction (q ≤ 0.1)
   
5. **Multicollinearity Removal** (276 → 100)
   - Iterative VIF (176 iterations)
   - VIF_max = 9.97
   
6. **Boruta Selection** (100 → 58)
   - Domain-invariant transformations
   - Final feature set

### Validation Strategy

- **Homology control**: CD-HIT at 60% identity
- **Cross-validation**: GroupKFold (5 splits)
- **Test set**: 32 proteins (176 residues)
- **No data leakage**: Protein-stratified splits

---

##  Data Sources

### PPI-HotSpotDB
The curated PPI-Hotspot+PDB^BM dataset is publicly available:
- **URL**: [http://ppihotspotdb.limlab.org/](https://ppihotspot.limlab.dnsalias.org/scripts/download.php)
- **Citation**: Chen et al., 2022
- **Content**: 918 residues from 158 proteins

### STING_RDB Descriptors
STING_RDB is a licensed resource maintained by Embrapa Digital Agriculture:
- **Access**: http://www.cbi.cnptia.embrapa.br/SMS
- **Contact**: Institutional credentials required

** Note**: You do **NOT** need direct STING_RDB access to use this repository. All processed descriptors (58 features) are included in `data/processed/`.

---

##  Selected Features

The final 58 features include:

| Category | Count | % |
|----------|-------|---|
| Interaction descriptors | 17 | 29.3% |
| Contact descriptors | 17 | 29.3% |
| Physicochemical properties | 10 | 17.2% |
| Graph-based metrics | 9 | 15.5% |
| Surface descriptors | 5 | 8.6% |

**Top-5 most important (SHAP):**
1. Cross_Link_Order_5_15_CA
2. Residue_Contacts_charge_repulsion
3. Density_Sponge_GN_density_4
4. Unused_Contacts_hydrophobic
5. Residue_Contacts_charge_attraction

---

##  Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

##  Contact

**Rodrigo Perez Verna**  
📧 rodrigo.verna@unesp.br  
🏛️ São Paulo State University (UNESP)

**Supervisors:**
- Dr. Ivan Mazoni (Embrapa Digital Agriculture)
- Dr. Goran Neshich (Embrapa Digital Agriculture)
- Prof. Dr. Geraldo Francisco Donegá Zafalon (UNESP)

---

##  Acknowledgements

This work was supported by:
- São Paulo Research Foundation (FAPESP) [grant 20/08615-8]
- São Paulo State University (UNESP) [PROPe grant 06/2023]
- Coordenação de Aperfeiçoamento de Pessoal de Nível Superior - Brasil (CAPES)

Special thanks to:
- Embrapa Digital Agriculture for STING_RDB access
- PPI-HotSpotDB consortium for curated dataset

---

##  License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

---

##  Related Resources

- [PPI-HotSpotDB](http://ppihotspotdb.com/)
- [STING_RDB](http://www.cbi.cnptia.embrapa.br/SMS)

---

## Project Status

-  Pipeline implemented


**Last updated**: January 2026
