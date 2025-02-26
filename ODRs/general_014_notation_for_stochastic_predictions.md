### **GitHub Issue: Standardizing Notation for Stochastic Predictions (ODR 014)**  

#### **Background & Context**  
In **Bayesian machine learning and probabilistic forecasting**, model predictions are often **stochastic**, meaning they represent **distributions over possible outcomes** rather than single point estimates. However, there is currently **no standardized notation** for:  

1. **Distinguishing deterministic predictions (`\hat{y}`) from stochastic predictions (`\tilde{Y}`)**  
2. **Representing individual samples from the posterior predictive distribution (`\tilde{y}^{(s)}`)**  
3. **Ensuring consistency across model outputs, documentation, API responses, and ensemble systems**  

To ensure **clarity, consistency, and best practices** across models, ensembles, APIs, and documentation, we establish **ODR 014 defining the standard notation for stochastic predictions**.  

---

### **📌 Decision & Deliverables**  

✅ **Deliverable 1 → ODR 014: Standardized Notation for Stochastic Predictions**  
- Defines **notation for deterministic vs. stochastic predictions**  
- Establishes **a mathematically rigorous, unambiguous convention**  
- Ensures alignment with **Bayesian inference and probabilistic ML best practices**  
- Applies to **model outputs, data storage, and API formats**  

✅ **Deliverable 2 → Implementation Across the Codebase**  
- Ensures consistent use of notation in **documentation, modeling code, and API responses**  
- Updates references in **existing ODRs (007–013) and mathematical formulations**  

---

### **📌 Proposed Notation**  

| Concept | Notation | Description |
|---------|----------|-------------|
| **Observed target** | \( y \) | True target value. |
| **Deterministic prediction** | \( \hat{y} \) | Point estimate (e.g., mean prediction from a frequentist model). |
| **Stochastic prediction** | \( \tilde{Y} \sim p(y \mid X, \mathcal{D}) \) | Random variable representing predictive uncertainty. |
| **One sample from \( \tilde{Y} \)** | \( \tilde{y}^{(s)} \) | A single draw from the posterior predictive distribution. |

✔ **Why Use \( \tilde{Y} \) for Stochastic Predictions?**  
- Clearly differentiates **stochastic predictions from deterministic predictions**  
- Aligns with **Bayesian probability notation**  
- Avoids confusion in ensemble forecasting and probabilistic ML models  

✔ **Why Use \( \tilde{y}^{(s)} \) for Samples?**  
- Explicitly denotes **individual Monte Carlo samples** from \( \tilde{Y} \)  
- Consistent with **notation used in variational inference and MCMC sampling**  

---

### **📌 API & Prediction Store Considerations**  

1️⃣ **Storage & Database Naming Conventions**  
- Should stored stochastic predictions **explicitly include the notation in column names**?  
- Example:  
  ```plaintext
  y_sample_1, y_sample_2, ..., y_sample_n
