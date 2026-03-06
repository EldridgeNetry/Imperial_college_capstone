### **Models Card - BBO Capstone Dataset**

# Overview
  - Name: Bayesian GP Optimiser for BBO Capstone
  - Type: Single-objective Bayesian optimisation with Gaussian Process surrogate and acquisition-based query selection
  - Version: v1.0 (10-round strategy, functions 1–8)

# Intended use

## Suitable tasks
  - Expensive, black-box functions with: limited evaluations (≈10–20), continuous inputs in [0,1]^d , noisy scalar outputs, and no gradients.
  - Iterative optimisation where each evaluation is costly (simulations, lab experiments, ML hyperparameters).

## Out-of-scope / avoid
  - High-throughput settings where random/evolutionary search is cheaper than BO overhead.
  - Problems with strong discontinuities, adversarial behaviour, or any structure that violates GP smoothness assumptions.
  - Multi-objective or constrained optimisation (not explicitly modelled here).

# Details: strategy across ten rounds

## Core method
  - Surrogate: Gaussian Process regression with RBF/kernel + noise term, refit after each new query.
  - Acquisition: primarily Expected Improvement (EI), with occasional checks using UCB/PI to assess stability.

## Evolution over rounds
  - Rounds 1–4:
    - Use default GP hyperparameters.
    - Broad exploration around initial design; simple UCB-based selection in under-sampled regions.
  - Rounds 5–7:
    - Tune noise variance using residuals vs predictive intervals, adjust length-scales per dimension for mid/high‑D functions.
    - Introduce “good vs bad” thresholding (top x%) and classification framing to understand decision boundaries.
    - Shift to more targeted exploration near uncertain but promising areas.
  - Rounds 8–10:
    - Focus on local refinement around best basins while keeping some acquisition mass on under-explored regions.
    - For lower‑dimensional functions: mainly exploitation and fine perturbations 
    - For higher‑dimensional functions: more cautious exploration due to sparse coverage and higher uncertainty.
   
# Performance

## Across eight functions
  - Objective: maximise each function with ≤14 evaluations (10th round = 10th new query).
  - Metrics used:
    - Best observed value per function.
    - Improvement over initial best (Δ best between initial dataset and latest round).
    - Qualitative: convergence patterns (plateau vs continued gains), stability of suggested region.

# Assumptions and limitations

## Key assumptions
  - Functions are smooth enough that a GP with chosen kernel can approximate them locally, and nearby points have correlated outputs.
  - Noise is homoscedastic and reasonably captured by a single noise variance term.
  - The evaluation budget is small, so surrogate-based guidance is preferred over exhaustive search.

## Limitations / failure modes
  - In high dimensions (6D, 8D), data is too sparse; GP may be overconfident along a few sampled directions and miss distant optima, potential global optimum.
  - If the function has sharp, narrow peaks or discontinuities, EI/UCB with a smooth kernel may not find them.
  - Sampling bias: early “lucky” high points attract many follow‑up queries, under-covering other areas.
  - No explicit modelling of constraints or multi-objectives

# Ethical considerations and transparency

## Transparency & reproducibility: (**)
  - The approach is documented via: repository structure, code for GP fit + acquisition  
  - This supports reproducibility and helps others adapt the strategy to different domains (e.g. bioprocess, materials).

## Responsible use:
  - Model card explicitly states intended use and limitations, reducing risk of over‑claiming “global optimum” or applying the method in unsuitable settings.
  - Practitioners are encouraged to treat results as decision support under uncertainty, not as guaranteed optima, mirroring best practices in responsible BO use for real experiments

------------------------------------------------------------------------------------------------------------------------------------------------------------

Open points log : 
[] fixed random seeds for reproducibility (**)
[] per‑round notes explaining each query choice.

