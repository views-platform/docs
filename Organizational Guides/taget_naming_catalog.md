# Target Naming Catalog  

| Catalog Info     | Details |
|-----------------|---------|
| **Subject**     | Forecast Target Naming Standard |
| **Version**     | 1.0 |
| **Status**      | Draft |
| **Author**      | Simon P. vdM |
| **Date**        | 26.02.2025 |
| **Owner**       | Simon P. vdM |
| **Review Date** | 26.03.2025 |

---

## 📌 Purpose  

This catalog defines the **standardized names** for target variables (`y`) used in the **VIEWS forecasting pipeline**.  

**Naming Convention:**  
```plaintext
[target_prefix]_[target_scope]_[target_version]
```

**Refer to:** 
- [ODR 007 - Transformation Prefix for Target Variables](https://github.com/views-platform/docs/blob/main/ODRs/general_007__transformation_prefix_for_target.md) 
- [ODR 008 - Prediction Prefix for Forecast](https://github.com/views-platform/docs/blob/main/ODRs/general_008_prediction_prefix_for_forecasts.md)  
- [ODR 009 - Target Naming Catalog](https://github.com/views-platform/docs/blob/main/ODRs/general_009_target_naming_catlog.md)  


---

## **Target Naming Table**  

### **💣 Conflict Events**  

| Target Name | Description | Source Dataset |
|------------|-------------|----------------|
| `sb`  | **State-based conflict fatalities (Best Estimate)** | UCDP GED |
| `sbhigh`  | **State-based conflict fatalities (High Estimate)** | UCDP GED |
| `sblow`   | **State-based conflict fatalities (Low Estimate)** | UCDP GED |
| `ns`  | **Non-state conflict fatalities (Best Estimate)** | UCDP GED |
| `nshigh`  | **Non-state conflict fatalities (High Estimate)** | UCDP GED |
| `nslow`   | **Non-state conflict fatalities (Low Estimate)** | UCDP GED |
| `os`  | **One-sided violence fatalities (Best Estimate)** | UCDP GED |
| `oshigh`  | **One-sided violence fatalities (High Estimate)** | UCDP GED |
| `oslow`   | **One-sided violence fatalities (Low Estimate)** | UCDP GED |

---

### **🚨 Political Stability & Risk Indicators**  

| Target Name | Description | Source Dataset |
|------------|-------------|----------------|
|... |... |... |

---

### **💹 Socioeconomic Indicators**  

| Target Name | Description | Source Dataset |
|------------|-------------|----------------|
|... |... |... |
---

### **🌡️ Environmental & Climate Factors**  

| Target Name | Description | Source Dataset |
|------------|-------------|----------------|
|... |... |... |
---

## 📌 Maintenance  

- Any **new targets** must be **proposed via a Pull Request** modifying this catalog.  
- Changes **must comply** with 

    - [ODR 007 - Transformation Prefix for Target Variables](https://github.com/views-platform/docs/blob/main/ODRs/general_007__transformation_prefix_for_target.md) 
    - [ODR 008 - Prediction Prefix for Forecast](https://github.com/views-platform/docs/blob/main/ODRs/general_008_prediction_prefix_for_forecasts.md)  
    - [ODR 009 - Target Naming Catalog](https://github.com/views-platform/docs/blob/main/ODRs/general_009_target_naming_catlog.md)  

