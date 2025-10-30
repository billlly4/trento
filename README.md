# 🧠 MRI Dementia Classification (OASIS-1)

This repository contains two complementary pipelines for classifying **dementia from MRI scans** of the **OASIS-1** dataset.

- **`final.ipynb` → ResNet18 embeddings + Logistic Regression**  
- **`main.ipynb` → Tissue volumes (GM/WM/CSF) + Classical ML**

---

## ⚙️ Overview

| Notebook | Goal | Input | Model | Output |
|-----------|------|--------|--------|---------|
| `final.ipynb` | Extract deep 512-D embeddings from MRI slices using pretrained ResNet18 and classify dementia | `.nii` MRI volumes + metadata Excel | ResNet18 + Logistic Regression (5×5 CV) | `results_resnet/resnet_embeddings.csv`, metrics JSON |
| `main.ipynb` | Parse tissue volumes from `_fseg.txt` and classify using engineered ratios + age | `*_fseg.txt` + metadata Excel | SVM / Logistic / RF (5×5 CV) | ROC-AUC comparison plot |

---

## 🧩 Requirements

```bash
pip install torch torchvision scikit-learn pandas numpy nibabel matplotlib seaborn tqdm joblib openpyxl
```

---

## 📁 Data

- MRI volumes stored in `disc1/`  
- Metadata file: `oasis_cross-sectional-5708aa0a98d82080.xlsx`  
- Optional tissue segmentation files: `*_fseg.txt` (CSF / GM / WM volumes)

Labeling rule:  
`1` → demented (`group` contains “dement”) or `CDR ≥ 0.5`  
`0` → control

---

## 🚀 Usage

```python
# in final.ipynb
ROOT = Path("disc1")
META_XLSX = Path("oasis_cross-sectional-5708aa0a98d82080.xlsx")
```
→ Extract ResNet18 slice embeddings → save 512-D features → evaluate Logistic Regression (5×5 CV).

```python
# in main.ipynb
ROOT = Path("disc1")
META = Path("oasis_cross-sectional-5708aa0a98d82080.xlsx")
```
→ Parse *_fseg.txt* tissue volumes → compute GM/WM/CSF ratios → evaluate SVM, Logistic Regression, and Random Forest → plot ROC-AUC comparison.

---

## 📊 Results

- Prints mean ± std of **ROC-AUC** and **Accuracy** (Repeated Stratified 5×5 CV).  
- `final.ipynb` saves outputs in `results_resnet/`.  
- `main.ipynb` displays a barplot comparing model AUCs.

---

## 🧠 Notes

- Excludes derived volumes (`seg`, `mask`, `fseg`, etc.).  
- Uses `class_weight="balanced"` for all models.  
- `SEED = 42` for reproducibility.  
- GPU supported automatically (`device = "cuda"` if available).
