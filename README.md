# Synthetic Data for Machine Learning: A Study on Quality and Evaluation

Code accompanying the Master's dissertation in Data Science at Iscte – University Institute of Lisbon (2026).

**Author:** Rebecca de Oliveira Cunha
**Supervisors:** Ana Maria de Almeida, Luís Nunes

## Overview

This repository contains the notebooks used to generate and evaluate synthetic tabular data for two imbalanced classification datasets. Two GAN architectures are compared, CTGAN and CTAB-GAN+, with SMOTE included only as a utility baseline.

The synthetic data is assessed along four pillars (utility, fidelity, semantic plausibility and privacy):

- **Manual evaluation.** Each `*_complete_pipeline.ipynb` notebook covers data preparation, baselines, generation, TSTR utility, TVD/KS fidelity, the He et al. (2025) seven-property imperceptibility audit, and memorisation risk (DCR/NNDR).
- **External validation.** Five independent frameworks are applied to each dataset: SynthEval, Synthcity, Anonymeter, SDMetrics and FEST.

## Datasets

| Dataset | File expected in `data/` | Source |
|---|---|---|
| Default of Credit Card Clients (30,000 rows) | `UCI_Credit_Card.csv` | UCI Machine Learning Repository (Yeh & Lien, 2009) |
| Bank Marketing (41,188 rows) | `bank-additional-full.csv` | UCI Machine Learning Repository (Moro et al., 2014) |

Both datasets are publicly available. The `data/` folder also contains every intermediate file produced by the pipelines, including the real splits, the synthetic datasets and the result tables. This means the evaluation notebooks can be run without retraining any model.

## Repository structure

```
├── credit_complete_pipeline.ipynb   # Credit: preparation, generation, manual evaluation
├── credit_syntheval.ipynb           # Credit: external validation (one notebook per framework)
├── credit_synthcity.ipynb
├── credit_anonymeter.ipynb
├── credit_sdmetrics.ipynb
├── credit_fest.ipynb
├── bank_complete_pipeline.ipynb     # Bank Marketing: same structure
├── bank_syntheval.ipynb
├── bank_synthcity.ipynb
├── bank_anonymeter.ipynb
├── bank_sdmetrics.ipynb
├── bank_fest.ipynb
├── scripts/                         # Notebooks that regenerate two dissertation figures from data/
├── data/                            # Raw data, splits, synthetic data, results (CSV)
└── figures/                         # Figures produced by the notebooks
```

## How to run

**Order.** For each dataset, run `*_complete_pipeline.ipynb` first, because it writes the files in `data/`. The five framework notebooks can then be run in any order, since each one only reads from `data/`.

**Environments.** The evaluation libraries have conflicting dependencies, so each framework runs in its own conda environment. The notebook metadata already selects the right kernel:

| Notebooks | Kernel / environment | Main packages |
|---|---|---|
| `*_complete_pipeline` | Python 3.12 (base) | ctgan, scikit-learn, imbalanced-learn, xgboost, torch |
| `*_syntheval` | `tese_eval` (Python 3.10) | syntheval |
| `*_synthcity` | `tese_synthcity` (Python 3.10) | synthcity 0.2.12, numpy < 2, opacus 1.4.0 |
| `*_anonymeter` | `tese_anonymeter` (Python 3.10) | anonymeter 1.1.0 |
| `*_sdmetrics` | `tese_sdmetrics` (Python 3.10) | sdmetrics 0.28.2 |
| `*_fest` | `tese_fest` (Python 3.10) | FEST (`privacy_utility_framework`, [synprivutil](https://github.com/Karo2222/synprivutil)) |

**CTAB-GAN+.** The pipelines use the official implementation, which is not included here. Clone it into the repository root before running the pipelines:

```
git clone https://github.com/Team-TUD/CTAB-GAN-Plus.git
```

## Reproducibility notes

- All splits and seeded operations use `RANDOM_STATE = 123` (70/30 stratified train–test split).
- The saved outputs of the two pipeline notebooks correspond to the final training run reported in the dissertation. Retraining the GANs, especially on different hardware, will produce slightly different synthetic data. Use the synthetic CSVs in `data/` to reproduce the reported results exactly.
- Some cells in the pipeline notebooks were re-executed individually during development, so their execution counts are not strictly sequential. The outputs shown are the final ones.
- CTAB-GAN+ internally reserves a stratified 20% of its input as a validation split (fixed seed 42 in the official code).
- The external validation uses the real minority class as reference, since the generators synthesise minority records only. For this reason, metrics that need a non-constant target, such as downstream classifiers or target correlations, are undefined and were excluded. Each notebook explains this where it applies.
- The notebooks in `scripts/` regenerate the fidelity (TVD/KS) and sensitivity (Property 4) figures in their final dissertation form, directly from the CSVs in `data/`.
- Anonymeter attacks are stochastic, so re-running them can change the risk estimates slightly within their confidence intervals.

## Citation

If you use this code, please cite the dissertation:

```
Rebecca de Oliveira Cunha (2026). Synthetic Data for Machine Learning: A Study on Quality
and Evaluation. Master's dissertation, Iscte – University Institute of Lisbon.
```

## License

The code is released under the MIT License. The datasets remain under the terms of their original sources.
