# DNA Melting Temperature Prediction under Mixed Salt Conditions
Thanks ChatGPT for the headstart - For detailed description, please read the Summary.pdf in the Results folder.

# Brief Description and Motivation

Accurate prediction of DNA melting temperature (Tm) is critical for applications such as PCR, where optimal annealing occurs within ~1–2 °C of Tm. However, predicting Tm for short DNA (<100 bp) in buffers containing both **monovalent (Na⁺)** and **divalent (Mg²⁺)** ions remains challenging due to complex, nonlinear salt dependencies.

This project develops a **data-driven, interpretable model** to predict Tm under mixed salt conditions using a **small dataset (516 samples)** while maintaining competitive accuracy with established models. 

---

# Literature Review

## Main Models and Datasets

* **Owczarzy et al. (2008)** developed state-of-the-art models using:

  * Linear regression with salt-dependent corrections
  * Decision-tree logic to select between formulas
  * More than 1000 experimental data points
  * Achieved ~1 °C MAE on independent test sets
* Limitations:

  * Complex decision rules
  * Nonlinear and parameter-dependent salt corrections
  * Reliance on nearest-neighbor thermodynamics (SantaLucia 1998)

## Other Proposed Models

* **Huguet et al. (2010, 2017)**:

  * Derived nearest-neighbor parameters from single-molecule experiments
  * Introduced **linear log-salt dependence per dinucleotide**
* Limitations:

  * Cannot unify mono + divalent salt conditions
  * Still relies on experimental thermodynamic inputs

---

# Our Goal

We aim to:

1. **Achieve high prediction accuracy (low MAE)** for mixed salt conditions using a small dataset
2. **Develop a single, global, interpretable model** that captures key biophysical dependencies

This project prioritizes the trade-off:

> **Accuracy ↔ Generality ↔ Interpretability**

---

# Models and Metrics

## Models Compared

* Linear Regression (LR)
* Generalized Additive Models (GAM)
* Decision Tree Regressor (DT)

## Metric

* **Mean Absolute Error (MAE)** (consistent with prior literature)

## Key Observations

* Salt dependence is **nonlinear**
* Feature interactions (especially salt × sequence features) are important
* GAM provides a balance between flexibility and interpretability

---

# Feature Engineering

We evaluate three feature sets:

## 1. Typical Features (Baseline)

* Duplex length
* GC content
* Salt concentrations (mono/divalent)

## 2. Naïve Features

* Fraction of A, T, C, G
* Duplex Length

## 3. Modern Features

* Dinucleotide frequencies (e.g., AA/TT, GC/CG, etc.)
* Duplex Length

## Additional Considerations

* Salt interaction terms:

  * `Mono × Feature`, `Div × Feature`
* Log-transformed salt concentrations (tested but not critical)

## Key Result

* **Best performance:**
  → *Typical features + salt interactions*

---

# Training and Cross-Validation

We evaluate generalization using three CV strategies:

## GC-Content CV (LOCO-GC)

* 3-fold (low, medium, high GC)

## Length CV (LOCO-LENGTH)

* 4-fold (15, 20, 25, 30 bp)

## Sequence CV (LOSO)

* 12-fold (leave-one-sequence-out)

## CV Conclusions

* Decision Trees → **overfitting**
* Modern features → too high dimensional for dataset size
* GAM > LR (captures nonlinear salt effects)
* Best model:

  > **GAM + Typical Features (+ salt interactions)**

---

# Testing

Models evaluated on:

## 1. Full Training Set (516 samples)

## 2. Independent Test Set (39 samples)

## 3. Filtered Test Set (20 samples within training length range)

## Final Result

* **LOCO-LENGTH model performs best across test scenarios**
* Comparable performance to Owczarzy (2008), but:

  * Uses **single global model**
  * Requires **less than half the data**

---

# Conclusions and Future Directions

## Key Achievements

* Developed a **single interpretable model** for mixed salt conditions
* Achieved **competitive MAE (~1–2 °C)**
* Identified **minimal feature set consistent with biophysics**
* Used limited number of data points!

## Future Work

1. **Expand dataset carefully**

   * Focus on length and GC-content diversity rather than redundant conditions
2. **Revisit modern (dinucleotide) features**

   * Requires larger dataset to avoid overfitting
3. **Model interpretability analysis**

   * Study GAM smooth functions and feature contributions
4. **Alternative ensembling approaches**

   * (e.g., voting models, though less interpretable)

---

# References

* SantaLucia (1998) – Nearest-neighbor thermodynamics
* Owczarzy et al. (2004, 2008) – Salt-dependent Tm models
* Huguet et al. (2010, 2017) – Single-molecule thermodynamics
* Vologodskii (2018) – DNA melting review
