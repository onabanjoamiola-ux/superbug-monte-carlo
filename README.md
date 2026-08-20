# Rise of the Superbugs — Monte Carlo Simulation of Antibiotic Resistance

A stochastic population dynamics study applying **branching process theory** and **Monte Carlo simulation** to model the survival and extinction of antibiotic-resistant superbug populations across discrete generations.

Built as part of the MSc Data Science programme at Liverpool John Moores University.
**Authors:** Amiola Onabanjo & Imariabe Enobakhare

---

## Overview

Antibiotic resistance is one of the most pressing public health challenges of the modern era. This project uses branching process theory to model how resistant bacterial populations grow, persist, or die out — and applies Monte Carlo simulation to estimate key population statistics with quantified uncertainty.

The central parameter governing long-term population fate is the expected number of offspring per individual, denoted **m**:

- **m > 1 (supercritical)** — the population has a positive probability of surviving indefinitely
- **m = 1 (critical)** — extinction is certain in the long run but may take many generations
- **m < 1 (subcritical)** — extinction is certain

Despite this theoretical clarity, individual population trajectories are inherently stochastic. Even supercritical populations can go extinct by chance — particularly when starting from a small number of individuals.

---

## Key Results

### Monte Carlo simulation (1,000 runs, Z₀ = 1, N = 100 generations)

| Metric | Value | 95% CI |
|---|---|---|
| Extinction probability (original superbug, m = 1.645) | 0.329 | [0.2999, 0.3581] |
| Extinction probability (critical superbug, m = 1.000) | 0.989 | [0.9825, 0.9955] |
| Mean extinction time (conditional on going extinct) | 1.599 generations | [1.489, 1.708] |
| Mean m̂ estimator (DNA: 1339, true m = 1.8) | 1.800 | [1.800, 1.800] |

### Effect of initial population size Z₀ on extinction probability

| Z₀ | Extinction probability |
|---|---|
| 1 | 0.342 |
| 5 | 0.004 |
| 10 | 0.000 |
| 20 | 0.000 |
| 50 | 0.000 |

A single surviving bacterium after antibiotic treatment is far more vulnerable to chance extinction than even a small colony of ten individuals — a direct practical implication for treatment strategy.

### Five DNA sequence comparison

| DNA Code | Classification | m | Extinction probability |
|---|---|---|---|
| 7699 | Supercritical | 1.645 | 0.331 |
| 7113 | Critical | 1.000 | 0.990 |
| 8111 | Subcritical | 0.546 | 1.000 |
| 6210 | Subcritical | 0.444 | 1.000 |
| 1239 | Supercritical | 2.333 | 0.074 |

---

## Methodology

### 1. Offspring distribution

The offspring distribution is derived from a four-digit DNA sequence. Each digit represents the unnormalised count of offspring 0, 1, 2, and 3. The sequence is normalised to give a probability distribution (p₀, p₁, p₂, p₃), and the expected offspring is calculated as:

```
m = p₁ + 2·p₂ + 3·p₃
```

Two superbugs are studied in detail:
- **Original (DNA: 7699)** — m = 1.645, supercritical
- **Critical (DNA: 7113)** — m = 1.000, critical threshold

### 2. Simulation framework

A branching process simulation tracks population size generation by generation. At each generation, each individual independently produces a random number of offspring sampled from the offspring distribution. The simulation terminates if the population reaches zero (extinction) or exceeds 10,000 individuals (computational cap for surviving populations).

### 3. Monte Carlo estimation

1,000 independent population trajectories are simulated per experiment to estimate:
- Extinction probability
- Distribution of extinction times
- Distribution of final and maximum population sizes
- Effect of initial population size Z₀ on extinction probability

### 4. Expected offspring estimator

The expected offspring m is estimated from simulated data using the ratio of total offspring to total parents across all generations. Only surviving populations are included, as extinct trajectories provide insufficient reproductive data. The estimator was validated against a known true value of m = 1.8, recovering it with zero rounding error.

### 5. Effect of simulation length

The m̂ estimator was evaluated across N = 10, 20, and 30 generations to assess convergence. At N = 10 the estimator overestimates true m with high variability (SD = 0.099). By N = 30 the estimate converges to the true value with SD = 0.038, confirming 30 generations is sufficient for reliable estimation.

### 6. Confidence intervals and relative errors

95% confidence intervals were computed for all key Monte Carlo estimates. A convergence plot confirms that 1,000 simulations is sufficient for stable estimation of the extinction probability. To achieve a 5% relative error, approximately 3,134 simulations would be required.

---

## Key Findings

**Extinction is an early-or-never event.** Populations that went extinct did so almost always within the first 2 generations. Beyond generation 2, surviving populations showed explosive growth consistent with m = 1.645 > 1.

**The m = 1 threshold is a critical boundary.** A seemingly small difference in m from 1.0 to 1.645 is the difference between 98.9% extinction probability and 32.9%. This has direct implications for antibiotic treatment — pushing a bacterial population below the m = 1 threshold is not merely a quantitative improvement but a qualitative phase change in survival dynamics.

**Initial population size matters independently of m.** Extinction probability drops sharply with Z₀ in the supercritical case, falling from 34.2% at Z₀ = 1 to effectively zero at Z₀ = 10. This finding goes beyond the simple m < 1 versus m > 1 classification and has practical implications for dosage design in antibiotic treatment.

**The m̂ estimator performs excellently on surviving populations.** The ratio estimator recovered the true m value with zero error when applied to non-extinct trajectories, confirming it as a reliable method for inferring reproductive fitness from observational data.

---

## Tech Stack

| Tool | Purpose |
|---|---|
| R | Primary language |
| R Markdown | Reproducible report with embedded code and output |
| base R (sample, hist, barplot) | Simulation and visualisation |
| knitr | Table rendering |

---

## Repository Structure

```
superbug-monte-carlo/
├── superbug_monte_carlo.Rmd    # Full reproducible R Markdown report
├── requirements.R              # Required packages
└── README.md
```

---

## Setup & Usage

### Requirements

```r
install.packages("knitr")
install.packages("rmarkdown")
```

### Running the report

Open `superbug_monte_carlo.Rmd` in RStudio and click **Knit** to generate the full PDF report with all simulations, plots, and results embedded. The document is fully self-contained — no external data files are required.

Note: the simulation runs 1,000 trajectories per experiment across multiple configurations. Knitting the full document takes approximately 2-5 minutes depending on hardware.

---

## What I Would Improve

- **Increase simulation count to 3,134** to achieve the 5% relative error threshold identified in the analysis
- **Add SMOTE-style oversampling for rare DNA sequences** to better explore the critical threshold region around m = 1
- **Extend to continuous-time branching processes** using birth-death models to better reflect real bacterial growth dynamics
- **Add Bayesian inference** for the m̂ estimator to quantify uncertainty with a posterior distribution rather than a point estimate

---

## Authors

**Amiola Onabanjo & Imariabe Enobakhare**
MSc Data Science — Liverpool John Moores University
[GitHub](https://github.com/onabanjoamiola-ux)
