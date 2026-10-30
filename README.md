# A Leakage-Aware Evaluation of Machine Learning Classifiers for Multi-Class Attack Detection in Wireless Sensor Networks

Companion code for the paper of the same title by **Muhammad Hanzala Dawood** (Department of Computer Science, University of Peshawar).

The notebook `WSN_DS_Leakage_Aware_Evaluation.ipynb` reproduces every figure, table and number reported in the paper.

## Overview

Seven classifiers (Logistic Regression, KNN, Decision Tree, Random Forest, XGBoost, RBF-kernel SVM and Gaussian Naive Bayes) are compared for five-class attack detection (Normal, Grayhole, Blackhole, TDMA, Flooding) on the WSN-DS dataset. The pipeline is designed to avoid data leakage:

- identifier columns (`id`, `who CH`, `Time`) are excluded from the model inputs;
- the 11% of records that are exact duplicates are removed **before** any split;
- feature scaling and SMOTE are fitted on training data only, inside every cross-validation fold.

Models are evaluated with stratified 5-fold cross-validation using accuracy, macro F1-score, per-class F1-score and computational cost. Ablation and robustness experiments test whether SMOTE and feature scaling actually help, whether the models depend on a node's cluster-head role, and whether they generalize to sensor nodes never seen in training.

## Key results

| Finding | Result |
|---|---|
| Best models | XGBoost and Random Forest are practically tied: 99.33% accuracy and 95.5% macro F1 for both |
| Cost | The XGBoost model is about 140 times smaller (1.3 MB vs 181 MB) and trains almost 3 times faster |
| SMOTE | Lowers macro F1 (Random Forest 96.7% to 95.5%): 24-41% fewer missed attacks, but more than twice as many false alarms |
| Feature scaling | No measurable benefit |
| Robustness | Macro F1 stays at 96.7% (XGBoost) on cluster-head records only and 95.3% on unseen sensor nodes |
| Hardest class | TDMA, consistent with earlier studies |

## Dataset

The WSN-DS dataset is not included in this repository. It was published by:

> I. Almomani, B. Al-Kasasbeh, and M. Al-Akhras, "WSN-DS: A Dataset for Intrusion Detection Systems in Wireless Sensor Networks," *Journal of Sensors*, vol. 2016, Article ID 4731953, 2016. [doi:10.1155/2016/4731953](https://doi.org/10.1155/2016/4731953)

Download `WSN-DS.csv` (374,661 records, 19 columns) from the original publication or a public mirror such as Kaggle.

## How to run

1. Open `WSN_DS_Leakage_Aware_Evaluation.ipynb` in [Google Colab](https://colab.research.google.com/).
2. Upload `WSN-DS.csv` to `/content/` (or change `DATA_PATH` in Section 1.3).
3. Run all cells from top to bottom.

To run locally instead, install the dependencies with `pip install -r requirements.txt`, set `DATA_PATH`, `ASSET_DIR` and `RUN_DIR` in Section 1.3 to local paths, and skip the Google Drive cell (Section 1.1) and the download cell (Section 9).

**Runtime.** Section 7 (cross-validation) takes about 1.5 hours on a Colab CPU runtime. Every finished model/fold is appended to `robustness_runs.csv`, so an interrupted run can be resumed: re-run Sections 1, 2, 4 and 7, and completed runs are skipped. Placing the provided `results/robustness_runs.csv` in the run directory skips Section 7 entirely and rebuilds the result tables in seconds.

## Notebook structure

| Section | Content | Paper output |
|---|---|---|
| 1. Setup | Configuration, readable attribute names, helper functions | |
| 2. Data loading | Quality checks, raw class distribution | |
| 3. Exploratory data analysis | Raw-data statistics | Attribute table, class-mean table, log-transform, correlation and box-plot figures |
| 4. Data cleaning | Identifier removal, de-duplication, cluster-head diagnostics | |
| 5. Held-out pipeline | 80/20 split, scaling, SMOTE, seven models | Class-distribution table |
| 6. Held-out analyses | Error analysis and significance testing | Confusion matrix, feature importance, McNemar test, SMOTE trade-off |
| 7. Cross-validation | Full pipeline plus four ablation/robustness settings | |
| 8. Results tables | Aggregation of all runs | Cross-validation, per-class and robustness tables |
| 9. Export | Zips all outputs | `paper_assets.zip` |

## Outputs

```
paper_assets/
├── figures/   fig_log_transform.png, fig_correlation.png, fig_boxplots_by_attack.png,
│              fig_confusion_xgb.png, fig_xgb_importance.png
├── tables/    features.tex, class_means.tex, cv_results.tex, per_class_cv.tex, robustness.tex
└── results/   robustness_runs.csv, mcnemar.txt, smote_precision_recall.csv
```

The `.tex` files are LaTeX `tabular` environments that the paper includes with `\input`.

## Reproducibility

All random processes use seed 42. Scores are deterministic for a given set of library versions (see `requirements.txt`); training and prediction times vary between runs and machines and are indicative only.

## Contact

Muhammad Hanzala Dawood: [muhammadhanzalahkhan1@gmail.com](mailto:muhammadhanzalahkhan1@gmail.com)
