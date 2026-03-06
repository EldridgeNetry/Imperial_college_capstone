## **Data Sheet - BBO Capstone Dataset**

# Motivation
This dataset is created as part of the Black‑Box Optimisation (BBO) Capstone to study data‑efficient optimisation of unknown, noisy functions under tight evaluation budgets.

It supports below two tasks.
- Primary task: maximise eight independent black‑box functions (2D–8D) over [0,1]d [0,1]d  with a limited number of queries.
- Secondary tasks: surrogate modelling (regression), “good vs bad” classification (via thresholding outputs), and analysis of exploration–exploitation strategies over time.

# Composition

- Contents
  - The dataset contains eight separate sub-datasets, one per function:
  - Function 1–8 with dimensionalities: 2D, 2D, 3D, 4D, 4D, 5D, 6D, 8D.
   - Each row corresponds to one query and includes:
     - Input vector  x ∈ [0,1]**d 
     - Scalar output y ∈ R 
     - Metadata (e.g. round index)

- Files and schema

Located under data/:
  -  function_1.csv
  -  function_2.csv
  -  …
  -  function_8.csv

Each file uses the following column schema (adapted per dimension):
  -  round – integer, optimisation round index (initial data as round 0)
  -  x1, x2, ..., xd – float, normalised inputs in [0,1] [0,1]
  -  y – float, observed function value
  -  note (optional) – free‑text comments about the query (e.g. rationale, anomalies)

-  Size and gaps
  -  Initial samples: 10–40 points per function (provided by the course).
  -  Additional queries: up to 10 points per function (rounds 1–10).
  -  Total per function: roughly 16–50 data points.

The sampling is not uniform over [0,1]**d :
  -  Denser clusters near early high‑value regions.
  -  Sparse coverage in distant regions, especially for higher‑dimensional functions (6D, 8D).
  -  Some feature interactions are under‑sampled because many queries vary only a subset of dimensions at a time.

3. Collection Process
Query generation
Initial data:

Provided by the capstone as a fixed starting design (10–40 points per function).

Sequential optimisation (rounds 1–10):

Fit a Gaussian Process surrogate to all available data for a given function.

Use a Bayesian optimisation acquisition function (primarily Expected Improvement; occasionally UCB/PI for comparison) to propose the next input.

Optionally review the suggestion to avoid trivial duplicates or obviously degenerate candidates.

Strategy over time
Rounds 1–3: exploratory—emphasis on under‑sampled regions with reasonable predicted values.

Rounds 4–7: balanced—tuned GP hyperparameters (noise, length‑scales) and, where useful, “good vs bad” thresholding to understand promising regions.

Rounds 8–10: refined—stronger focus on local optimisation around the best‑known basins, while still allocating some queries to uncertain areas.

Time frame
Data were collected in ten iterative rounds per function, following the capstone schedule (e.g. one new query per function per round).

The temporal order matters: each new query is conditioned on all previous observations via the surrogate and acquisition.

4. Preprocessing and Uses
Preprocessing
Inputs:

All inputs are already scaled to 
[
0
,
1
]
[0,1]; no additional scaling applied in the stored CSVs.

Outputs:

Raw outputs are stored as provided by the BBO oracle.

In analysis notebooks, outputs may be normalised or standardised for modelling; these transformations are local to the analyses, not persisted to the main CSV files.

Derived fields (in notebooks or auxiliary files):

“Good vs bad” labels based on output percentiles (e.g. top 20% as good=1).

Feature importance measures, residuals, and uncertainty summaries.

Intended uses
Evaluating and comparing optimisation strategies (e.g. different acquisition functions, hyperparameters).

Training and assessing surrogate models for regression and classification.

Studying exploration–exploitation trade‑offs and data‑efficiency under small budgets.

Educational demos for black‑box optimisation and sequential experimental design.

Inappropriate uses
As a large-scale benchmark for generic ML tasks (dataset is small and highly specific).

For any real‑world decision‑making (the underlying functions are synthetic/course-specific).

For statistical analyses that assume i.i.d. sampling (the data are actively selected via an optimiser).

5. Distribution and Maintenance
Location
The dataset is stored in this repository under the data/ directory.

It is intended for:

Course staff (assessment and review)

Peers (feedback, learning)

Potential future employers (portfolio), subject to course policies.

Terms of use
Provided under the terms of the capstone programme and hosting platform.

Intended primarily for educational and demonstrative purposes.

Any reuse should:

Acknowledge the BBO capstone context.

Note that the underlying functions and initial samples were course‑provided.

Maintenance
Maintainer: student author of this repository.

Changes (e.g. corrections, additional queries) should be documented in:

CHANGELOG.md, and/or

Versioned filenames (e.g. function_1_v2.csv).
