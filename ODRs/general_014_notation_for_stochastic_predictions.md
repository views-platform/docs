# **ODR 014: Standardizing Notation for Stochastic Predictions**  

| **ODR Info**      | **Details** |
|-------------------|------------|
| **Subject**      | Standardized notation for deterministic and stochastic predictions |
| **ODR Number**   | 014 |
| **Status**       | Proposed |
| **Category**     | General |
| **Author**       | Simon P. vdM |
| **Date**         | 26.02.2025 |
| **Owner**        | Simon P. vdM |
| **Review Date**  | 26.02.2025 |

---

## **📌 Context**  

In **Bayesian machine learning and probabilistic forecasting**, model predictions are often **stochastic**, meaning they represent **distributions over possible outcomes** rather than single point estimates. However, there is currently **no standardized notation** for:  

1. **Distinguishing deterministic predictions ($\hat{y}$) from stochastic predictions ($\tilde{Y}$)**  
2. **Representing individual samples from the posterior predictive distribution ($\tilde{y}^{(s)}$)**  
3. **Denoting the full set of stochastic samples ($\{ \tilde{y}^{(s)} \}_{s=1}^{n}$)**  
4. **Handling multi-output models where multiple target variables are predicted ($\tilde{Y}_j$)**  
5. **Explicitly linking deterministic predictions to stochastic predictions via expectation or MAP estimates**  

To ensure **clarity, precision, and consistency** across models, ensembles, documentation, and research papers, we establish **ODR 014 defining the standard notation for stochastic predictions**.

---

## **✅ Decision**  

All **deterministic and stochastic predictions** must follow a **structured and standardized notation** for consistency across documentation, codebases, and research papers.  

1. **A stochastic prediction ($\tilde{Y}$) represents a full predictive distribution.**  
2. **A deterministic prediction ($\hat{y}$) is derived from a stochastic prediction using either the expectation ($\mathbb{E}[\tilde{Y}]$) or the MAP estimate ($\tilde{y}_{\text{map}}$).**  
3. **Each stochastic prediction is composed of $n$ samples ($\{ \tilde{y}^{(s)} \}_{s=1}^{n}$) drawn from the posterior predictive distribution.**  
4. **Multi-output models must use target indices ($\tilde{Y}_j$) to distinguish multiple prediction targets.**  

---

## **📌 Proposed Notation**  

| **Concept** | **Notation** | **Description** |
|-------------|-------------|----------------|
| **Observed target** | $y$| True observed value for a given input. |
| **Deterministic prediction** | $\hat{y} $| Single point estimate |
| **Stochastic prediction** | $\tilde{Y} \sim p(y \mid X, \mathcal{D})$| Predictive distribution over possible outcomes. |
| **One sample from $\tilde{Y}$** | $\tilde{y}^{(s)}$ | A single Monte Carlo sample from $\tilde{Y}$. |
| **Full set of samples** | $\{ \tilde{y}^{(s)} \}_{s=1}^{n}$ | A set of $n$ posterior samples approximating $\tilde{Y}$. |
| **Deterministic estimate from $\tilde{Y}$** | $\hat{y} = \mathbb{E}[\tilde{Y}]$ | Expected value of stochastic prediction. |
| **Maximum A Posteriori (MAP) estimate** | $\tilde{y}_{\text{map}} = \arg\max_{y} p(y \mid X, \mathcal{D})$ | Most probable predicted value. |
| **Multi-output model prediction** | $\tilde{Y}_j$ | Stochastic prediction for target index $j$. |

---

## **📌 Linking Stochastic and Deterministic Predictions**  

Deterministic predictions are often derived from stochastic distributions. We formally define:  
$$
\hat{y} = \mathbb{E}[\tilde{Y}] \quad \text{or} \quad \hat{y} = \tilde{y}_{\text{map}}
$$
where:  
- $\mathbb{E}[\tilde{Y}] = \frac{1}{n} \sum_{s=1}^{n} \tilde{y}^{(s)}$ is the **expected value** (mean).  
- $\tilde{y}_{\text{map}} = \arg\max_{y} p(y \mid X, \mathcal{D})$ is the **Maximum A Posteriori (MAP) estimate**.  

🔹 **Other forms of summarization (e.g., HDIs, quantiles) are outside the scope of this ODR** and are defined in **ODR 013 (Summarization of Stochastic Predictions).**  

---

## **📌 Formal Definition of Posterior Predictive Distribution**  

To avoid ambiguity, we explicitly define the **posterior predictive distribution**, which governs stochastic predictions:  
$$
p(y \mid X, \mathcal{D}) = \int p(y \mid \theta, X) p(\theta \mid \mathcal{D}) d\theta
$$
where:  
- $p(y \mid X, \mathcal{D})$ is the **distribution over future predictions**, given the observed dataset $\mathcal{D}$.  
- $p(y \mid \theta, X)$ is the **likelihood of $y$** given a model with parameters $\theta$.  
- $p(\theta \mid \mathcal{D})$ is the **posterior distribution over parameters** after observing $\mathcal{D}$.  

A stochastic prediction $\tilde{Y}$ is then sampled from this posterior predictive distribution.

---

## **📌 Consequences**  

### **✅ Benefits**  
✔ **Ensures a single, clear notation across all documentation, models, and research papers.**  
✔ **Reduces ambiguity when comparing deterministic and stochastic predictions.**  
✔ **Clarifies how multi-output models handle stochastic predictions.**  
✔ **Aligns with Bayesian probability notation and ML best practices.**  

### **⚠ Considerations**  
- This notation **must be consistently applied across all ODRs** and model documentation.  
- Some models may require adaptation to explicitly differentiate deterministic and stochastic predictions.  

