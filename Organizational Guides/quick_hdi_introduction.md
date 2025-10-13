
# **Internal Discussion Document: Highest Density Intervals (HDI)**

**Prepared for:** VIEWS DataLab (MD&D+Infra)  
**Purpose:** Internal circulation and discussion on the use of HDIs (in Python) for summarizing zero-inflated or skewed posterior distributions, reducing data volume, and conveying tail-risk events.

---

## Table of Contents
1. [Introduction](#introduction)  
2. [What is an HDI?](#what-is-an-hdi)  
3. [Why Use HDIs Instead of Fixed Percentiles?](#why-use-hdis-instead-of-fixed-percentiles)  
4. [Detailed Example: Skewed or Zero-Inflated Data](#detailed-example-skewed-or-zero-inflated-data)  
5. [Multiple Modes: Handling Rare Bimodality](#multiple-modes-handling-rare-bimodality)  
6. [How to Compute an HDI (in Python)](#how-to-compute-an-hdi-in-python)  
   - [Posterior vs. Predictive Distributions](#posterior-vs-predictive-distributions)  
   - [Code Snippet: A Quick Demonstration](#code-snippet-a-quick-demonstration)  
7. [Practical Tips & Tail-End Uncertainty](#practical-tips--tail-end-uncertainty)  
   - [Data Storage & Sharing](#data-storage--sharing)  
8. [Common Pitfalls & Misunderstandings](#common-pitfalls--misunderstandings)  
9. [Conclusion & Next Steps](#conclusion--next-steps)  
10. [References & Further Reading](#references--further-reading)

---

## Introduction

This document aims to:

- Provide a **comprehensive yet practical** explanation of Highest Density Intervals (HDIs).
- Compare HDIs to percentile-based intervals (e.g., 5th, 25th, 50th, 75th, 95th).
- Show how HDIs can **reduce data volume** needed for sharing forecast uncertainty (instead of sending thousands of samples).
- Address **zero-inflated** and **tail-focused** scenarios common in our work.
- Emphasize **Python workflows** (e.g., `ArviZ`, `PyMC`), with **hands-on examples**.

We encourage team members to review, discuss, and decide how best to apply HDIs in ongoing projects.

---

## What is an HDI?

A **Highest Density Interval (HDI)**—sometimes called a Highest Posterior Density Interval (HPDI)—is a **Bayesian credible interval** with two main properties:

1. **Specified Proportion of the Posterior**  
   It contains a chosen proportion (e.g., 50%, 90%, 95%) of the total probability under the posterior distribution.

2. **Highest Density**  
   Every point inside the HDI has a **higher posterior density** than any point outside. In other words, the HDI is the *tightest* interval (or intervals) that captures the designated probability mass.

By focusing on *density*, HDIs naturally highlight **where the distribution is most “concentrated.”**

> **Key takeaway:**  
> A 90% HDI means: “There is a 90% posterior probability that the true value lies in this tightest (most probable) region of the distribution, given our model and priors.”

---

## Why Use HDIs Instead of Fixed Percentiles?

1. **Focus on the Most Probable Region**  
   - Percentiles (e.g., 5%–95%) can be wide, especially with zero-inflated or highly skewed data.  
   - HDIs capture the “peak(s)” of the distribution, excluding low-density tail areas if they’re not that probable.

2. **Reduced Misinterpretation**  
   - With zero-inflation, you might have a huge spike at 0 plus a long right tail. A percentile approach could place intervals that don’t fully clarify that large spike.  
   - HDIs explicitly show that 0 is part of the densest region, if relevant.

3. **Practical Summaries**  
   - Instead of sending 10k samples to end users, you can send a couple of HDIs (e.g. 50%, 95%, 99% HDI), giving a clear, concise representation of likely outcomes.

4. **Flexibility for Tail Analysis**  
   - If you need more coverage on rare events, you can adjust the interval coverage (e.g., 99.99% HDI) or complement HDIs with explicit tail probabilities.

---

## Detailed Example: Skewed or Zero-Inflated Data

Imagine a forecast for **fatalities** that often remains *zero* (no event) but occasionally spikes to larger values.

### Using Percentiles (5%, 25%, 50%, 75%, 95%)
- 5%: 0  
- 25%: 0  
- 50% (Median): 0  
- 75%: 5  
- 95%: 40  

**Interpretation**  
- Most of the early percentile cutoffs are at 0, which might not illustrate how *much* of the mass is at zero versus the tail.

### Using HDIs (e.g., 50%, 95%)
- 50% HDI: [0, 0] (indicating half of the distribution is literally at zero if there’s a strong spike)  
- 95% HDI: [0, 30]

**Interpretation**  
- “Half the outcomes (50%) are **exactly zero** (the highest-density location).”  
- “We’re 95% sure it falls between 0 and 30.” (The upper bound might be narrower or wider depending on how long the tail extends.)

This direct highlight of zero inflation can be **more transparent** than spread-out percentiles that keep repeating zero for 5%, 25%, and 50%.

---

## Multiple Modes: Handling Rare Bimodality

While zero-inflation is common, multi-modal distributions are less frequent but can still arise. If you have two distinct peaks (e.g., one at 0, another around 20), an HDI can split into two intervals around each peak, giving a clearer picture of the distribution shape.

> **Note**: This scenario is uncommon in our domain, but the capacity for HDIs to reflect *disjoint intervals* is still advantageous.

---

## How to Compute an HDI (in Python)

1. **Obtain Posterior Samples**  
   - Typically via MCMC in [PyMC](https://www.pymc.io/) or another Bayesian framework.

2. **Use a Python Library**  
   - **ArviZ**: `arviz.hdi()` can compute HDIs for specified probability masses.  
   - **PyMC**: Some PyMC versions integrate with ArviZ seamlessly or provide built-in functions to compute HDIs.

3. **Check Zero‐Inflated or Multi‐Modal Cases**  
   - The function should handle large spikes at zero.  
   - Confirm that your HDI method can detect multi-modal intervals if needed.

4. **Validate Convergence**  
   - As always, ensure your MCMC chains are converged (e.g., via R‐hat, ESS) before summarizing with HDIs.

### Posterior vs. Predictive Distributions
- **Parameter HDIs**: Summarize where model parameters (like coefficients) lie.  
- **Predictive HDIs (what we need)**: Summarize the distribution of *forecasted outcomes*—e.g., the likely range of casualties or some other event.  
  - For zero-inflation (or tail events), you’ll typically be looking at **posterior predictive** HDIs.

### Code Snippet: A Quick Demonstration

Below is a minimal example using PyMC + ArviZ to illustrate how we might compute HDIs in practice:

```python
import pymc as pm
import arviz as az
import numpy as np

# Generate some zero-inflated data for demonstration
np.random.seed(42)
data = np.concatenate([np.zeros(50), np.random.poisson(lam=5, size=50)])

with pm.Model() as model:
    # Let's assume we have a mixture model or a simpler approach:
    # For demonstration, a single Poisson - real model might differ for zero inflation
    lam = pm.Exponential('lam', 1.0)
    obs = pm.Poisson('obs', mu=lam, observed=data)
    
    # Sample from the posterior
    trace = pm.sample(2000, tune=1000, target_accept=0.95)
    
    # Posterior predictive for forecasting (optional)
    ppc = pm.sample_posterior_predictive(trace, return_inferencedata=True)

# Compute a 95% HDI for 'lam' (parameter) and for the posterior predictive
hdi_lam = az.hdi(trace, var_names=['lam'], hdi_prob=0.95)
hdi_ppc = az.hdi(ppc, var_names=['obs'], hdi_prob=0.95)

print("Parameter HDI (95%):\n", hdi_lam)
print("Predictive HDI (95%):\n", hdi_ppc)
```

- The **Parameter HDI** reveals where our rate (\(\lambda\)) lies.  
- The **Predictive HDI** (`obs`) reveals the likely range of the outcome.  
- In what we do, only the latter is relevant. 

---

## Practical Tips & Tail-End Uncertainty

1. **Report a Few Key Intervals**  
   - We likely want to share a **50% HDI** (central region), a **95% HDI** (standard “wide” credible range), and a **99%** HDI for extreme coverage (total of six new columns if we split each interval into lower and upper bound).

2. **Include Tail Probabilities**  
   - If rare but impactful events matter — e.g., “exceeding 1000 fatalities” — consider explicitly reporting `P(X > threshold)`.  
   - HDIs focus on the densest region, so a separate tail probability highlights risk of catastrophic events.

### Data Storage & Sharing
- **Why**: We want to avoid sending users thousands of samples.  
- **How**: Provide each coverage interval as two columns or keys, e.g.:
  - `hdi_50_lower`, `hdi_50_upper`  
  - `hdi_95_lower`, `hdi_95_upper`  
  - `hdi_99_lower`, `hdi_99_upper`


---

## Common Pitfalls & Misunderstandings

1. **Zero-Inflation Overlooked**  
   - If half the data is zero, don’t be surprised by an HDI that’s [0,0] for narrower coverage. It can feel odd but is *correct*.

2. **Forgetting Tail Communication**  
   - HDIs skip low‐density extremes. If extremes matter, include them via tail probabilities or 99.9% HDIs.

3. **Misinterpretation of “Most Probable”**  
   - The HDI is about density, not the only possible region. Values outside the HDI can still occur—just with lower probability.

---

## Conclusion & Next Steps

- **HDIs** provide a concise, density‐focused summary of zero‐inflated, skewed, or multi‐modal posterior distributions.
- They **reduce** data volume needed to communicate uncertainty—no need to send thousands of raw samples when a few HDIs plus some tail probabilities suffice.
- **Next Steps**:
  1. **Pilot**: Use HDIs in an uncertainty aware forecast.  
  2. **Standardize**: Decide which intervals (50%, 95%, 99%) to produce. Possibly add `P(X>threshold)` for tail risk.  
  3. **Educate**: Ensure end‐users understand how to interpret an HDI (especially if zero inflation is high).

---

## References & Further Reading

- **PyMC**: [https://www.pymc.io/](https://www.pymc.io/) – Bayesian modeling & MCMC in Python.  
- **ArviZ**: [https://python.arviz.org/](https://python.arviz.org/) – Diagnostics, plotting, and HDI calculations.  
- **Kruschke, J. K.** _Doing Bayesian Data Analysis_ – (Ch. 2.3) Practical steps for Bayesian analysis & HDIs.  
- **Gelman, A. et al.** _Bayesian Data Analysis_ – Overviews of credible intervals, referencing highest density regions.  
