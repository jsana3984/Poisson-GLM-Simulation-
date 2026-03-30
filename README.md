# Poisson GLM Simulation
R-based statistical modeling projects demonstrating Poisson regression, overdispersion handling, and Monte Carlo simulations for count data analysis.

## Overview
**Project Highlight:** *Poisson GLM Simulation (R, grade : 95) – Built count-data models with offsets and overdispersion diagnostics; ran Monte Carlo study on confounding bias.*

This repository contains the R code and statistical analysis for two related studies in count-data modeling. 
1. **Mental Time Travel Analysis:** Fitting Generalized Linear Models (GLMs) to psychological Experience Sampling Method (ESM) data to predict rates of future-oriented thoughts, including formal overdispersion correction.
2. **Lucia de Berk Simulation:** A 200-iteration Monte Carlo simulation of a famous legal case to mathematically demonstrate the severe impact of omitted variable bias (confounding) on Type I error rates.

## Part 1: Count-Data Modeling & Overdispersion
The first analysis models the rate of future thoughts reported by subjects. Because participants answered a variable number of signals, raw counts could not be directly compared. 

### Mathematical Formulation
A Poisson regression model was formulated where the expected count $\mu_i$ for subject $i$ is modeled as:

$$
\log(\mu_i) = \beta_0 + \beta_1 \cdot \text{Age}_i + \beta_2 \cdot \text{Sex}_i + \beta_{traits} \cdot \text{Traits}_i + \log(t_i)
$$

The term $\log(t_i)$ serves as an **offset** with a fixed coefficient of 1, effectively transforming the model to predict the *rate* ($\mu_i / t_i$) rather than the absolute count.

### Overdispersion Diagnostics
The standard Poisson model assumes $Var(Y) = E[Y]$. We formally tested for overdispersion by calculating the dispersion parameter $\phi$:

$$
\phi = \frac{\chi^2}{df_{residual}}
$$

The diagnostic yielded $\phi \approx 1.564$, indicating significant overdispersion. To correct for this and prevent underestimated standard errors, the model was refitted using a **Quasipoisson** family, scaling up the standard errors by $\sqrt{\phi}$ to ensure valid, conservative statistical inference.

## Part 2: Monte Carlo Study on Confounding Bias
The second analysis simulates the flaw in the statistical reasoning used during the real-world trial of Lucia de Berk. The simulation generates artificial hospital shift data where incident rates are driven by the *type* of shift (e.g., morning shifts have higher baseline risks), completely independent of the nurse on duty.

### Simulation Methodology
Three distinct analytical approaches were tested over 200 replications to evaluate their Type I error rates (falsely rejecting the null hypothesis $\beta_{Lucia} = 0$):

1. **Careful Analysis (Poisson GLM):** Controls for the confounder (shift type). 
   $$\log(\mu) = \beta_0 + \beta_1 \cdot \text{Morning} + \beta_2 \cdot \text{Lucia}$$
2. **Less Careful Analysis (Poisson GLM):** Aggregates data and ignores the confounder. 
   $$\log(\mu) = \beta_0 + \beta_2 \cdot \text{Lucia} + \log(\text{Shifts})$$
3. **Original Trial Analysis (Hypergeometric):** Calculates the exact probability of observing $q$ or more incidents, falsely assuming incident risks are constant across all shifts. 
   $$P(X \ge q) = \sum_{x=q}^{k} \frac{\binom{m}{x} \binom{n}{k-x}}{\binom{m+n}{k}}$$
### Key Results
The Monte Carlo study empirically proved the danger of omitted variable bias:
* The **Careful Analysis** maintained the correct nominal Type I error rate (**~0.050**).
* The **Less Careful Analysis** yielded a drastically inflated false positive rate (**~0.455**).
* The **Original Analysis** used in the actual trial yielded the worst false positive rate (**~0.520**), demonstrating how ignoring statistical confounders can lead to severe real-world consequences.

## Files in this Repository
* `StatProjectSana.Rmd`: The complete R Markdown file containing data processing, modeling, and the Monte Carlo simulation loops.
* `StatProjectSana.pdf`: The compiled statistical report detailing the descriptive statistics, mathematical formulations, and final rejection rate tables.

## Tech Stack
* **Language:** R
* **Libraries:** `dplyr`, `MASS`, `haven`
* **Statistical Concepts:** Generalized Linear Models (GLMs), Poisson Regression, Quasipoisson, Offset Variables, Overdispersion, Monte Carlo Simulation, Confounding Bias, Hypergeometric Distribution, Stepwise AIC Selection.
