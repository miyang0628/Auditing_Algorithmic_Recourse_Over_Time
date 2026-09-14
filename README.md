# Auditing Algorithmic Recourse Over Time

A longitudinal, causal, and actuarial audit of counterfactual-explanation (CFE)
recourse, instantiated on a nationally representative six-wave health panel
(2019–2024). This repository contains the full, reproducible analysis behind the
manuscript's five-axis framework — from realisability and equity through to
causal non-identification and the pricing consequence of an unverified
risk-improvement credit.

> **Anonymous repository.** This repository accompanies a manuscript currently
> under double-blind review. Author names, affiliations, funding, and the target
> venue have been removed to preserve anonymity. A de-anonymised version with
> full citation details will be released after review.

---

> **TL;DR** — Algorithmic recourse (“change feature *j* by *δ* and you'll be
> approved”) is almost always validated *cross-sectionally*: does the prescribed
> change satisfy feasibility constraints and flip the model *now*? Following the
> same people over six annual waves, we show why that is not enough. Recourse is
> **mis-dosed** (the prescribed change is realised by <1% of the population); its
> **causal effect is not identifiable** once temporal ordering is enforced —
> a verdict that holds across **five independent identification strategies**; and
> its **attainment is persistently unequal** across age and income. We make the
> stake concrete: a credit priced on the model's promised risk reduction
> **under-prices the net premium by ~15%**, while the naive reading of the raw
> association would misprice in the *opposite* direction. Certify recourse with a
> longitudinal evidence panel, not a generation-time feasibility check.

---

## The five-axis audit

Recourse validity is decomposed into five axes that form an **ordered chain of
necessary conditions** — each is meaningful only if the previous one holds.

| Axis | Question | Method |
|------|----------|--------|
| **1. Realisability** | Does the prescribed change actually occur in the population, and at what magnitude? | Realised `t→t+1` change vs. prescribed magnitude; achieved-quantile |
| **2. Causal validity** | Among those who realise it, does ground-truth risk fall? | Target-trial emulation with a **lagged design**, corroborated by four further strategies: covariate adjustment, propensity-score matching, individual fixed effects, five-year cohort tracking, and an **event study with a parallel-trends test** |
| **3. Actuarial significance** | Is the risk difference material for the book, and what does an unverified credit cost? | Adverse-selection proxies (utilisation / OOP-cost / private-insurance) with FDR control; **net-premium mispricing** `Δπ = p·s·(δ_model − δ_true)` |
| **4. Boundary calibration** | Are feasibility bounds evidence-based? | Empirical, age-conditional bounds from realised change |
| **5. Longitudinal equity** | Is recourse comparably attainable across groups, over time — and for whom is it feasible at all? | Attainment rates by stratum; gap-trend test; population-wide, guardrail-constrained **recourse-feasibility map** and age×BMI threshold matrix |

---

## Headline findings

- **Mis-dosing (Axis 1).** The counterfactual generator prescribes a median BMI
  reduction of **4.34 units**; only **0.5%** of at-risk adults realise a
  reduction that large within one year (**0.7%** at two years). The 90th
  percentile of realised one-year reduction is just **1.06 units**.
- **Causal non-identification (Axis 2).** A large, robust *contemporaneous*
  association (hypertension OR **0.61**) **collapses under a lagged design**
  (OR **1.03**, 95% CI 0.78–1.36) that enforces exposure-before-outcome, and the
  same null is reached independently by covariate adjustment, propensity-score
  matching, individual fixed effects, five-year tracking, and an event study
  with a satisfied parallel-trends test. Because the lagged design is
  under-powered (minimum detectable OR ≈ 0.67 or 1.49), we claim
  **non-identification, not absence of effect**. Diabetes behaves symmetrically
  (lagged OR **0.92**), dissolving an apparent *harmful* contemporaneous signal.
- **Pricing consequence (Axis 3).** Onset is costly (incremental annual medical
  expenditure ≈ **354k** for hypertension, **572k** for diabetes). At the at-risk
  baseline rate, the net premium is ≈ **10,300**; a credit granted on the model's
  promised ~15% reduction prices the life at ≈ **8,800**, so with a verified
  reduction of ~0 the per-policy under-pricing is ≈ **1,550 (~15%)**. The naive
  reading of the raw (positive) association would instead *load* reducers —
  a mispricing of the opposite sign.
- **Unequal attainment & feasibility (Axes 4–5).** Only **7.6%** of at-risk
  adults realise a recourse-consistent change; attainment is lowest for older
  and lower-education groups and the gap is **stable, not closing**
  (trend slope ≈ 0, *p* = 0.89). Guardrail-constrained batch scoring computes
  recourse for the entire top-risk group in ~1 second: only **19%** can reach the
  median risk line, feasibility falling from **64%** (ages 45–54) to **~0%**
  (75+), so behaviour-linked instruments are appropriate only for the borderline
  segment.
- **Robustness.** Findings survive richer propensity models, a
  utilisation-anchored outcome, IPCW attrition weighting, multiple imputation,
  cross-model concordance (Spearman ρ = 0.72), out-of-time validation, and
  BH false-discovery control. A negative-control outcome (ER use) is associated
  with treatment contemporaneously *and* under the lagged design (OR ≈ 1.38),
  locating residual confounding in an **outcome-specific** healthcare-engagement
  construct rather than one that manufactures a spurious onset effect.

---

## Repository structure

The analysis is organised as two self-contained, sequential pipelines that share
the same panel and the same conclusions. Keeping them separate preserves exact
reproducibility: each pipeline has its own configuration, derived-data schema,
and execution order, and each writes its own `results/`.

```
.
├── part1_audit/                 # Five-axis audit: realisability, causal (lagged
│   │                            #   target trial), actuarial proxies, boundaries,
│   │                            #   equity, and the full robustness battery
│   ├── notebooks/               # 00–11, run in order
│   ├── results/{figures,tables}
│   └── data/                    # place panel wave files here (NOT redistributed)
│
├── part2_verification/          # Multi-strategy causal verification + pricing:
│   │                            #   fixed effects, five-year tracking, event
│   │                            #   study/parallel trends, net-premium mispricing,
│   │                            #   population-wide feasibility & threshold matrix
│   ├── notebooks/               # 00–14, run in order
│   ├── results/{figures,tables}
│   ├── requirements.txt
│   └── data/raw/                # place raw panel + medical-cost files here
│
├── LICENSE                      # MIT (code only)
└── README.md
```

### How the two pipelines map onto the framework

| Framework element | Where it is computed |
|---|---|
| Axis 1 realisability, Axis 4 age-conditional bounds | `part1_audit/03_axis1_axis4` |
| Axis 2 lagged target trial, Axis 3 adverse-selection proxies | `part1_audit/04_axis2_axis3`, `07`, `10`, `11` |
| Axis 5 attainment equity + gap trend | `part1_audit/05_axis5`, `10` |
| Diabetes target, washout, negative control, E-value, IPCW, MI, cross-model, FDR | `part1_audit/06`–`09` |
| Corroborating strategies: fixed effects, five-year tracking | `part2_verification/06`, `12` |
| Event study + parallel-trends test | `part2_verification/13` |
| Net-premium severity & mispricing (`Δπ`) | `part2_verification/14` |
| Population-wide feasibility + age×BMI threshold matrix | `part2_verification/08`, `11` |
| Diabetes replication, out-of-time validation | `part2_verification/07`, `09`, `10` |

---

## Reproducing the analysis

### 1. Data access

The micro-data are **not redistributed** here; they are governed by the data
provider's data-use agreement. The panel is a publicly documented, nationally
representative six-wave household health survey (2019–2024); access-request
procedures are described on the provider's official portal.

- **`part1_audit`** expects the six waves' individual files and the codebook in
  `part1_audit/data/`:

  ```
  a_ind.sas7bdat  b_ind.sas7bdat  c_ind.sas7bdat
  d_ind.sas7bdat  e_ind.sas7bdat  f_ind.sas7bdat
  dict.xlsx
  ```

- **`part2_verification`** expects the raw SAS files under
  `part2_verification/data/raw/y{year}/` with the wave-prefix naming
  (`a_ind.sas7bdat` … `f_ind.sas7bdat`, plus the corresponding `*_ms.sas7bdat`
  needed only to rebuild medical-cost severity). Its notebook `01` rebuilds the
  intermediate `*.parquet` files consumed by notebooks `02`–`14`.

### 2. Environment

Python 3.12. `part2_verification` ships a pinned `requirements.txt`; the audit
pipeline uses the same stack:

```bash
# Option A — pinned (recommended)
pip install -r part2_verification/requirements.txt

# Option B — explicit
pip install pandas==2.2.3 numpy pyreadstat pyarrow xgboost scikit-learn \
            dice-ml statsmodels seaborn matplotlib openpyxl jupyter nbconvert
```

> **Note.** `pandas==2.2.3` and `dice-ml` are pinned for compatibility with the
> counterfactual search used to generate recourse.

### 3. Run

Each pipeline is executed in notebook order and writes to its own `results/`.

```bash
# Part 1 — five-axis audit (00 → 11)
cd part1_audit/notebooks
for nb in $(ls *.ipynb | sort); do
  jupyter nbconvert --to notebook --execute --inplace "$nb"
done

# Part 2 — verification + pricing + feasibility (01 → 14; 01 rebuilds parquet)
cd ../../part2_verification/notebooks
for nb in $(ls *.ipynb | sort); do
  jupyter nbconvert --to notebook --execute --inplace "$nb"
done
```

---

## Design decisions worth knowing

- **Targets.** Hypertension (primary) and diabetes (parallel). Onset is defined
  from disease-free-at-*t* → diagnosed-at-*t+κ*, with the diagnosis-timing field
  used to purge onsets that predate baseline. Dyslipidaemia is excluded from the
  longitudinal analysis — it is not coded consistently across 2019–2023 — and
  appears only as a 2024 cross-sectional illustration.
- **Actionable features.** BMI, regular exercise, walking days, and a
  **harmonised** drinking-frequency ordinal (the survey item is renamed and
  rescaled mid-panel: `D1` in 2019–2022, `D1_2` in 2023–2024). Smoking enters
  the risk model as a predictor (combined lifetime + current-type definition,
  ≈15.6% prevalence) but is **not** used as a causal treatment — its skip
  structure leaves the realised-quit sample too thin.
- **Directional recourse.** DiCE is constrained to the risk-reducing direction
  (BMI/drinking may only fall, activity may only rise) via one-sided permitted
  ranges anchored at each applicant's baseline, and by realism guardrails
  (≤15% relative BMI reduction, floor 18.5, binary snapping, boundary re-check).
- **Why a lagged design.** Contemporaneous exposure/outcome windows let
  subclinical disease drive both weight change and diagnosis. The lagged design
  (exposure `t→t+1`, onset `t+1→t+2` among those disease-free at `t+1`) is the
  temporally honest specification, applied symmetrically to both targets and to
  the negative control.
- **Why five strategies.** Because achieving a BMI reduction is self-selected,
  no single estimator is decisive. Covariate adjustment, propensity-score
  matching, individual fixed effects, five-year cohort tracking, and an event
  study with a parallel-trends test rest on different, largely non-overlapping
  assumptions; their agreement on the null is the evidential logic of Axis 2.
- **Non-identification, not “no effect.”** The lagged/within-person designs are
  under-powered; a TOST equivalence test does not establish equivalence. We
  therefore claim the causal effect is *not identified* in these data once
  temporal ordering is enforced — the sharp and defensible form of the result.

---

## Figure & table conventions

All figures are greyscale, 600 dpi, saved as both PNG and PDF with no captions
(captions live in the manuscript); this keeps them print-ready and
colour-blind-safe. Tables are CSV (UTF-8). Each pipeline writes to its own
`results/figures/` and `results/tables/`.

---

## A note on interpretation

The specific null — self-reported annual BMI and physician-diagnosed onset in
one national panel — should **not** be read as “weight loss does not matter”;
randomised trials show intentional weight loss lowers blood pressure and diabetes
incidence. The point is about the **inference**: a risk-improvement credit must
be justified by the effect of the *measurable, incentivisable* change on the
*recorded* outcome, in the data an insurer actually uses to price. The
equivalence analysis states this precisely — the data exclude a protective effect
of the promised magnitude, rather than proving an effect of exactly zero.

---

## Citation

> Citation details are withheld during double-blind review. Please cite the
> anonymous manuscript as referenced in the submission system; a full citation
> will be added here upon acceptance.

## License

Code is released under the MIT License (see `LICENSE`). The panel micro-data are
**not** covered by this license and remain subject to the data provider's
data-use agreement.

## Acknowledgements

Acknowledgements and data-source attribution are withheld to preserve anonymity
during review, and will be restored in the de-anonymised release.
