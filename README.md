# FIFA World Cup 2026 — Possession & Group-Stage Advancement

An inferential-statistics analysis testing whether teams that advanced past the FIFA World Cup 2026 group stage had a significantly higher average ball possession than teams eliminated in the group stage.

---

## Table of Contents

- [Overview](#overview)
- [Research Question](#research-question)
- [Key Findings](#key-findings)
- [Repository Structure](#repository-structure)
- [Data](#data)
- [Methodology](#methodology)
- [Results](#results)
- [Assumptions & Limitations](#assumptions--limitations)
- [Getting Started](#getting-started)

---

## Overview

Possession is one of the most widely reported football statistics, yet its link to results is contested: low-possession, counter-attacking sides have won major tournaments before. This project tests the claim empirically using group-stage data from the 2026 World Cup rather than assuming the answer in advance.

The analysis covers the full workflow of an analytic task: question formulation, data wrangling, sampling, descriptive statistics, confidence interval estimation, and hypothesis testing.

> **Note on variable choice:** The analysis originally planned to use pass completion %, but this metric was not published for the tournament on FBref. Possession % was adopted instead as a distinct measure of game *control* rather than technical *accuracy*.

## Research Question

> Do teams that advanced past the group stage have a significantly higher average ball possession percentage than teams eliminated in the group stage?

**Hypotheses** (two-sided, α = 0.05)

- **H₀:** Mean possession % of advanced teams = mean possession % of eliminated teams
- **Hₐ:** Mean possession % of advanced teams ≠ mean possession % of eliminated teams

## Key Findings

| Group      | n  | Mean possession | Std. dev. |
| ---------- | -- | --------------- | --------- |
| Advanced   | 30 | 53.03%          | 13.21     |
| Eliminated | 30 | 42.67%          | 12.04     |

- **95% CI** for the Advanced group's mean possession: **48.31% – 57.76%**
- **Welch two-sample t-test:** t = 3.18, p = 0.0024 → reject H₀
- Teams that advanced averaged roughly **10 percentage points** more possession in the sampled matches.

## Repository Structure

```
fifa-wc2026-possession-analysis/
├── data/
│   ├── raw_data.csv             # Manually collected possession % per team per match
│   └── round_of_32_teams.csv    # The 32 teams that reached the Round of 32
├── notebooks/
│   └── Analysis.ipynb              # Notebook version of the analysis
├── .gitignore
├── requirements.txt
└── README.md
```

## Data

| File                           | Columns                                    | Description                                                                                                                                                                                                                                |
| ------------------------------ | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `data/raw_data.csv`          | `team`, `match_id`, `possession_pct` | 144 team-match records (one row per team per group-stage match), collected manually from match reports ([fbref.com/en/comps/1/schedule/World-Cup-Scores-and-Fixtures](https://fbref.com/en/comps/1/schedule/World-Cup-Scores-and-Fixtures)) |
| `data/round_of_32_teams.csv` | `team`                                   | The 32 teams that advanced to the Round of 32                                                                                                                                                                                              |

**Unit of analysis:** one team in one group-stage match (team-match).

**Populations**

- **Advanced:** 96 team-match records — 24 group winners/runners-up plus 8 best third-placed teams (32 teams × 3 matches)
- **Eliminated:** 48 team-match records (16 teams × 3 matches)

The advanced/eliminated label is **not typed in by hand**. It is attached programmatically by merging the match data against the 32-team reference list, which is more reliable and auditable than tagging 144 rows manually.

## Methodology

The notebook follows six steps:

1. **Load** the match data and Round-of-32 reference list.
2. **Clean & wrangle** — strip stray whitespace from team names (71 rows affected), merge to create the `advanced` flag, and validate that all 32 reference teams match and that population sizes are 96 / 48.
3. **Sample** — simple random sample of n = 30 from each population, without replacement, with a fixed seed (`RANDOM_SEED = 42`) for reproducibility.
4. **Descriptive statistics** — mean, median, variance, standard deviation, range, and IQR per group, plus a side-by-side boxplot.
5. **Confidence interval** — 95% CI for the mean possession of the Advanced group (z-based, computed step by step and cross-checked with `statsmodels`).
6. **Hypothesis test** — two-sample t-test (Welch, unequal variances) following the State → Plan → Solve → Conclude process, with histograms used to check normality.

## Results

**Descriptive statistics (samples of n = 30)**

| Statistic              | Advanced      | Eliminated    |
| ---------------------- | ------------- | ------------- |
| Mean                   | 53.03         | 42.67         |
| Median                 | 54.50         | 40.00         |
| Sample variance        | 174.38        | 144.85        |
| Std. deviation         | 13.21         | 12.04         |
| Range                  | 53.00         | 51.00         |
| IQR                    | 18.50         | 13.25         |
| 25th / 75th percentile | 43.25 / 61.75 | 34.25 / 47.50 |

**Inference**

- Standard error (Advanced): 2.41; margin of error: 4.73
- t* = 3.18, p = 0.0024 (< 0.05) → **reject the null hypothesis**

**Conclusion:** In this tournament's sampled matches, teams that advanced past the group stage had significantly higher average possession than teams eliminated in the group stage, and the gap (~10 percentage points) is practically meaningful as well as statistically detectable.

## Assumptions & Limitations

- **Random sampling** — satisfied via simple random sampling without replacement.
- **Normality** — the Advanced sample is roughly symmetric; the Eliminated sample is mildly right-skewed. With n = 30 per group, the Central Limit Theorem makes the t-test reasonably reliable, but the skew is noted as a minor limitation.
- **Non-independence** — each team contributes three group-stage matches, so observations within a group are not fully independent.
- **Correlation, not causation** — possession is measured over the whole match and is influenced by game state (a leading team may sit deeper; a trailing team may chase the game), so the relationship may partly reflect scoreline effects.
- **Scope** — conclusions apply to the FIFA World Cup 2026 group stage, not football in general.
- **Data collection** — possession values were collected manually from match reports.

## Getting Started

### Prerequisites

- Python 3.9+
- Packages listed in `requirements.txt` (`pandas`, `numpy`, `scipy`, `statsmodels`, `matplotlib`, `jupyter`)

### Clone and install

```bash
git clone https://github.com/ashoktamang002/fifa-wc2026-possession-analysis.git
cd fifa-wc2026-possession-analysis

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### Run the notebook

```bash
jupyter notebook notebooks/Analysis.ipynb
```

---

## Author

**Ashok Tamang** — Master of Data Science, Charles Darwin University
