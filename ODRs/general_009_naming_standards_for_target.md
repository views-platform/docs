
# ODR 009: Standardizing Target Naming for Observed & Forecasted Values (`y`, `ŷ`)  

| ODR Info        | Details |
|----------------|---------|
| **Subject**    | Standardizing Target Naming for Observed & Forecasted Values (`y`, `ŷ`) |
| **ODR Number** | 009 |
| **Status**     | Proposed |
| **Category**   | General |
| **Author**     | Simon P. vdM |
| **Date**       | 26.02.2025 |
| **Owner**      | Simon P. vdM |
| **Review Date**| 26.03.2025 |

---

## 📌 Context  

The **VIEWS forecasting pipeline** aggregates predictions from multiple models into an **ensemble model**. Each prediction model produces **Prediction DataFrames** containing forecasted values (`ŷ`) for specific target variables (`y`).  

For the ensemble model to correctly **match and process** predictions from different models, **target variable names must be standardized**. Without a clear naming convention:  
- **Forecasted target columns may mismatch**, leading to incorrect ensemble calculations.  
- **Evaluation pipelines may fail** if expected target names do not align.  
- **New models may generate misaligned forecast outputs, complicating ensemble integration.**  

To prevent these issues, **a standardized Target Naming Catalog is introduced** to ensure consistent naming across all forecasting models.  

---

## ✅ Decision  

All target variables must follow a **consistent, structured naming convention**:  
```plaintext
[prediction_prefix]_[target_transformation]_[target_name]
```

Where:  
- **Observed values (`y`) → No prefix.**  
- **Forecasted values (`ŷ`) → Prefix with `pred_`.**  
- **Target transformation** → Specifies applied transformation (e.g., `ln_` for log transformation).  
- **Target name** → Standardized variable name from the Target Naming Catalog.  

All naming conventions are maintained in the [Target Naming Catalog](https://github.com/views-platform/docs/blob/main/Organizational%20Guides/target_naming_catalog.md).  
All transformations can be found in [ODR 007 - Transformation Prefix for Target Variables](https://github.com/views-platform/docs/blob/main/ODRs/general_007__transformation_prefix_for_target.md).  
Information on the prediction prefix can be found in [ODR 008 - Standardizing Forecast Naming Conventions (`y` → `ŷ`)](https://github.com/views-platform/docs/blob/main/ODRs/general_008_prediction_prefix_for_forecasts.md).  

---

## ⚠ Error Handling & Validation  

The **VIEWS pipeline** follows a **Fail-Fast Philosophy**, meaning:  
- **Incorrectly named targets will cause an ERROR.**  
- **Automated validation** will enforce compliance at the pipeline level.  

If a model provides a non-compliant target name, an error message will be raised:  
```plaintext
ERROR: [DATE_TIME] [MODEL_NAME] Target variable '<variable_name>' does not comply with the naming convention.
Expected format: [prediction_prefix]_[target_transformation]_[target_name].
Refer to the [Target Naming Catalog](https://github.com/views-platform/docs/blob/main/Organizational%20Guides/target_naming_catalog.md) and [ODR 009](https://github.com/views-platform/docs/blob/main/ODRs/general_009_target_naming.md) for valid names.
```

---

## 🔄 Governance & Approval Process  

- Any **new target name** must be **proposed via a GitHub Pull Request** modifying the **Target Naming Catalog**.  
- The **proposal will be reviewed** by the **infrastructure team** within **one month of submission**.  
- The **author of the proposal** must:  
  - Justify the addition of the new target.  
  - Ensure the name follows catalog conventions.  
  - Provide supporting documentation (e.g., dataset metadata).  

