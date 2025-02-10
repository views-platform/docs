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
7. [Practical Tips & Tail-End Uncertainty](#practical-tips--tail-end-uncertainty)  
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
- Emphasize **Python workflows** (e.g., `ArviZ`, `PyMC`).

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
   - Instead of sending 10k samples to end users, you can send a three HDIs (e.g. 50%, 95%, 99% HDI), giving a clear, concise representation of likely outcomes.

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
   - Typically via MCMC in PyMC or another Bayesian framework.

2. **Use a Python Library**  
   - **ArviZ**: `arviz.hdi()` can compute HDIs for specified probability masses.  
   - **PyMC**: Some PyMC versions integrate with ArviZ seamlessly or provide built-in HDI functions.

3. **Check Zero‐Inflated or Multi‐Modal Cases**  
   - The function should handle large spikes at zero.  
   - Confirm that your HDI method can detect multi-modal intervals if needed.

4. **Validate Convergence**  
   - As always, ensure your MCMC chains are converged (e.g., via R‐hat, ESS) before summarizing with HDIs.

---

## Practical Tips & Tail-End Uncertainty

1. **Report a Couple of Intervals**  
   - We should report the 50% HDI for the “most likely zone,” plus a 90% and/or 95% HDI for broader coverage.

2. **Include Tail Probabilities**  
   - If rare but impactful events matter — like “exceeding 100 fatalities” — we could report report `P(X > 1000)` as a separate stat (here return periods will also be helpful, but that is a different matter).  
   - HDIs focus on the densest region, but we can still want to highlight the *extreme tail risk*.

3. **Visual Aids**  
   - Provide a density or histogram plot with the HDI region shaded. Show the zero spike if present.

4. **Communicate Why**  
   - Emphasize that you are *not ignoring* extreme outcomes; the HDI might exclude them if they’re low-probability.  
   - If stakeholders need explicit tail info, a separate tail probability is easy to add.

---

## Common Pitfalls & Misunderstandings

1. **Zero-Inflation Overlooked**  
   - Always confirm whether your distribution has a notable spike at zero. The HDI might be [0,0] for a 50% coverage, which can look odd at first glance but is correct if half the samples are indeed zero.

2. **Forgetting Tail Communication**  
   - HDIs can skip very low-density extremes. Since these extremes are critical, we might want to highlight them explicitly.

---

## Conclusion & Next Steps

- **HDIs** offer a concise, density‐focused summary of zero‐inflated, skewed, or even multi‐modal posterior distributions.
- They **help reduce** the data volume needed to communicate uncertainty - no need to send thousands of raw samples if a few HDIs (and maybe some tail probabilities) suffice.
- **Next Steps**:
  1. **Adopt** HDIs using `ArviZ` or (more likely) `PyMC`.  
  2. **Standardize** on a couple intervals (e.g., 50%, 95%, and 99% HDI) plus perhaps later an explicit tail risk stats for high-impact events.  
  3. **Educate** end‐users on interpreting HDIs so they understand “where the mass is,” especially in zero‐inflated contexts.

---

## References & Further Reading

- **PyMC**: [https://www.pymc.io/](https://www.pymc.io/) – Bayesian modeling and MCMC in Python.  
- **ArviZ**: [https://python.arviz.org/](https://python.arviz.org/) – Diagnostics, plots, and HDI calculations.  
- **Kruschke, J. K.** _Doing Bayesian Data Analysis_ – section 2.3. THE STEPS OF BAYESIAN DATA ANALYSIS.
- **Gelman, A. et al.** _Bayesian Data Analysis 3_ – Overviews of credible intervals, referencing highest density regions.

