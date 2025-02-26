# ODR 008: Standardizing Forecast Naming Conventions (`y` → `ŷ`)  

| ODR Info        | Details |
|----------------|---------|
| **Subject**    | Standardizing Forecast Naming Conventions (`y` → `ŷ`) |
| **ODR Number** | 008 |
| **Status**     | Proposed |
| **Category**   | General |
| **Author**     | Simon P. vdM |
| **Date**       | 26.02.2025 |
| **Owner**      | Simon P. vdM |
| **Review Date**| 26.03.2025 |

---

## 📌 Context  

The **VIEWS forecasting pipeline** generates predicted values (`ŷ`) for various target variables (`y`). However, no enforced convention exists for naming these predictions, leading to inconsistencies across models.  

While **[ODR 007 - Transformation Prefix for Target Variables](https://github.com/views-platform/docs/blob/main/ODRs/general_007__transformation_prefix_for_target.md)** defines transformation prefixes (`ln_`, `by_`), it does **not** establish a consistent convention for **predicted values (`ŷ`)**.  

### **Problem Statement**  
The lack of a standardized naming convention for predicted values (`ŷ`) introduces multiple risks:  
- **Inconsistent Data Processing**: Forecast outputs vary across models, making downstream analysis harder.  
- **Pipeline Errors**: Without a clear distinction, models may misinterpret forecasted values.  
- **Evaluation Challenges**: **Ensemble reconciliation** and validation require a consistent naming scheme.  
- **Scalability Issues**: Future transformations and prediction methodologies need a **clear, extensible structure**.  

To mitigate these risks, **this ODR establishes a formal naming standard for predicted values (`ŷ`)** that integrates seamlessly with ODR 007.  

---

## ✅ Decision  

All **predicted forecast values (`ŷ`)** must adhere to the following **structured naming convention**:  
1. **Prefix all forecasted values with `pred_`** to distinguish them from raw target variables.  
2. **Apply transformation prefixes from ODR 007** (e.g., `ln_`, `by_`) when applicable.  
3. **Use a consistent target variable name**, referencing the upcoming **forecast target naming catalog**.  

### **Naming Convention**  
```plaintext
pred_[transformation_prefix]_[target_name]
```

### **Transformation Prefixes (from ODR 007)**  
| Prefix | Meaning | Transformation Details |
|--------|---------|------------------------|
| `lr_` | **Linear** | Raw, untransformed values |
| `ln_` | **Natural Log of target + 1** | `log(1 + target)`, allowing zero handling |
| `lx_` | **Natural Log of target + exp(-100)** | `log(exp(-100) + target)`, alternative zero handling |
| `by_` | **Binary** | Constrained to {0,1} |

### **Example Target Names ([Target Naming Catalog](https://github.com/views-platform/docs/blob/main/Organizational%20Guides/taget_naming_catalog.md))**  
| Target_name | Target Details |  
|-------------|---------------|  
| `sb_` | **UCDP GED State-Based Violence (Best)** |  
| `sbhigh_` | **UCDP GED State-Based Violence (High)** |  
| `sblow_` | **UCDP GED State-Based Violence (Low)** |  
| `os_` | **UCDP GED One-Sided Violence (Best)** |  
| `ns_` | **UCDP GED Non-State-Based Violence (Best)** |  

### **Examples of Full Predicted Target (`ŷ`) Names**  
| Forecast Type  | Naming Convention Example |
|---------------|-------------------------|
| Linear Prediction | `pred_lr_sb` (Linear forecast for state-based conflict fatalities) |
| Log-Transformed Prediction | `pred_ln_os` (Log-transformed forecast for one-sided conflict fatalities) |
| Binary Prediction | `pred_by_sbhigh` (Binary forecast for state-based conflict fatalities (high estimate)) |

✔ **`pred_` prefix first** → Clearly distinguishes predictions from raw target variables.  
✔ **Transformation prefixes follow ODR 007** → Ensures consistency across models.  
✔ **Explicit target naming catalog integration** → Standardizes all forecast target names.  

---

## 🔧 Implementation Plan  

### **Immediate Actions**  
1️⃣ **Create a branch** for drafting the ODR and implementing changes.  
2️⃣ **Refactor all forecasting models** to comply with the new naming standard: See GitHub issues **[Refactor Prediction Naming StepShifter](...)** and **[Refactor Prediction Naming HydraNet](...)**.  
3️⃣ **Update data documentation** to ensure clarity and consistency.  

### **Governance & Validation**  
- **Automated Validation:** Enforce naming convention at the pipeline level.  
- **Refactoring Strategy:** Existing models must be **retrofitted** for compliance.  
- **Forecast Target Naming Catalog:** A **centralized reference** for all forecast targets will be created.  

---

## ⚠ Error Handling & Logging Strategy  

The **VIEWS machine learning pipeline** follows a **Fail-Fast Philosophy**, meaning:  
- **Incorrectly named forecast variables will cause an ERROR** in model execution.  
- **Automated validation** will check compliance before model submission.  

If a model fails due to incorrect naming, the system will output a **clear error message**:  
```plaintext
ERROR: [DATE_TIME] [MODEL_NAME] Forecast variable '<variable_name>' does not comply with the naming convention.
Expected format: pred_[transformation_prefix]_[target_name].
Valid prefixes: pred_, lr_, ln_, lx_, by_.
For correct target names, see [Target Naming Catalog (TBD)]().
```
This ensures that **researchers, developers, and maintainers** can quickly diagnose and resolve issues.  

---

## 📌 Consequences  

### **✅ Positive Effects**  
- **Prevents Silent Failures**: Forecasting pipelines will correctly interpret predictions.  
- **Improves Model Transparency**: Ensures predictions are clearly labeled and traceable.  
- **Enables Automated Validation**: Ensures compliance **before models are processed**.  
- **Enhances Model Interoperability**: Allows seamless integration across forecasting models.  

### **⚠ Negative Effects**  
- **Refactoring Overhead**: Existing models and querysets must be updated.  
- **Additional Validation Complexity**: Requires new validation logic to enforce naming rules.  
- **Potential Backward Compatibility Issues**: External dependencies using previous forecast names may need updates.  

---

## 🔄 Governance & Approval Process  

- Any **new transformation prefix** must be **proposed via a GitHub Pull Request** updating this ODR.  
- The **researcher/developer** proposing changes is responsible for:  
  - Updating this ODR.  
  - Defining **what the transformation does**.  
  - Providing an example of **correct prefix usage**.  
- If the transformation applies only to **a specific model class**, it **does not require an infrastructure change**.  
- If the transformation affects the **viewser infrastructure**, it **must be reviewed by the infrastructure team**.  


