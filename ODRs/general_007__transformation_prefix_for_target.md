# general_007_target_variable_naming ODR

| ODR Info           | Details                                |
|--------------------|----------------------------------------|
| Subject            | Transformation prefixes for target     |
| ODR Number         | 007                                    |
| Status             | proposed                               |
| Category           | general                                |
| Author             | Jim Dale                               |
| Date               | 13/02/2025                             |
| Owner              | Jim Dale                               |
| Review Date        | 14/02/2025                             |


---

## Context
Although the VIEWS database stores only raw linear features, the VIEWS pipeline is able to accept models with target variables of fundamentally different character, in particular the model may predict the target variable, the logarithm thereof, or a binary representation of the target variable (e.g. where all values > 0 are coded 1, zeros are coded 0 and negative values do not occur or are not permitted), with necessary transformed performed by viewser before the data enters the pipeline. It is likely that other transformations of target variables may be required in the future, e.g. z-transformed or Fourier-transformed. Current transformations include:

- Natural logarithm of the target variable + 1 (log(1 + target)).
- Natural logarithm of the target variable + exp(-100) (log( exp(-100) + target)).
- Binary representations (target > 0 → 1, target = 0 → 0).

**Problem Statement:**  There is at present no enforced convention indicating whether the target variable has undergone a transform. The need for one is now acute, since this lack of standardization creates critical risks in the system, particularly for:

- The **ensemble point-reconciliation algorithm** needs to know if the variables it is attempting to reconcile are log or linear, otherwise silent errors will result. 
- The same is true for the future **ensemble distribution-reconciliation algorithm**. 
- The **reconciliation of some transformed targets may be entirely inappropriate** - the reconciliation machinery needs to be able to detect if it is presented with such variables so that it can throw a suitable error.
- To **ensure correct evaluation and model transparency**, we most ensure that the correct transformation is reported and the correct evaluation used.
- This is also needed for **future compatibility**; i.e. it will allow additional transformations to be introduced without ambiguity.


## Decision
All target variables **must have names prefixed with 'xx_', where 'xx' is a two-letter code indicating what type the variable is**, and which must be a member of a set of permitted prefixes, which can be added to or amended, but which currently consists of


### **Mandatory Prefixes** _(initial set, expandable)_  
| Prefix | Meaning | Transformation Details |
|--------|---------|------------------------|
| `lr_` | **Linear** | Raw, untransformed values |
| `ln_` | **Natural Log of taget + 1** | `log(1 + target)`, allowing zero handling |
| `lx_` | **Natural Log of taget + exp(-100)** | `log(exp(-100) + target)`, alternative zero handling |
| `by_` | **Binary** | Constrained to {0,1} |

**New transformations** must be proposed **GitHub Pull Request proposing an update to this ODR**.  

### **Multiple Transformations**  
Each **unique transformation combination** will by treated as a **new distinct transformation** and receive its own **distinct prefix**.  For example, a **log-transformed, z-standardized** variable would use a **new prefix** (e.g., `zl_`).  

This **ODR** will be maintained to ensure consistency.  

### **Error Handling & Logging Strategy**  
The VIEWS machine learning pipeline is meant to operated as critical digital infrastructure and thus adheres to a **Fail-Fast Philosophy**. As such,  **incorrect target variable naming will cause an ERROR.** This aligns with the **logging strategy outlined in views-pipeline-core [ADR-020](https://github.com/views-platform/views-pipeline-core/blob/main/documentation/ADRs/020_log_files_and_realtime_alerts.md)**.  

### **Automated Validation & Error Messaging**

(NOT YET IMPLEMENTED se [issue #17](https://github.com/views-platform/views-pipeline-core/issues/17) )

- **Checks** will be implemented to ensure:  
  - Target variable names follow the correct prefix convention.
  - Model configs (e.g. `config_queryset`, `config_meta`) match the naming standard.  
- **Non-compliant models will be blocked from submission to the prediction store and evaluation.**  
- If a model fails due to incorrect naming, the system will output a **clear error message**:  

  ```
  ERROR: [DATE_TIME] [MODEL_NAME] Target variable '<variable_name>' does not comply with the naming convention.
  Expected format given config_meta.py: <prefix>_<variable_name>. 
  Format extracted using config_queryset.py: <prefix>_<variable_name>.   
  Valid prefixes: lr_, ln_, lx_, by_.
  Please refer to the [ODR-007](https://github.com/views-platform/docs/tree/main/ODRs/general_007_target_variable_naming.md) documentation for correct usage.
  ```
This ensures researcher, developers, and maintainers **immediately understand the issue and how to fix it**.  

### Governance & Approval Process 
- Any **new transformation** must be **proposed via the standard GitHub Pull Request** review process pertaining to this ODR.  
- The **researcher/developer** proposing the transformation is responsible for:  
  - Updating this ODR.  
  - Defining **what the transformation does**.  
  - Providing an example of **correct prefix usage**.  
- If the transformation is **model-specific** (applied only within a single model class such as [veiws-stepshifter](https://github.com/views-platform/views-stepshifter) or [views-hydranet](https://github.com/views-platform/views-hydranet)), the researcher can implement it directly **without requiring infrastructure changes**.  
- If the transformation is to be part of the **viewser** infrastructure, it **must be reviewed by the infra team** before implementation.  



## Overview
---

### Consequences
**Positive Effects:**
Enforcing this naming convention will:  
- **Prevent Silent Failures**: Ensures that all parts of the pipeline can correctly interpret target variable types, avoiding errors caused by inappropriate mathematical operations (e.g., summing logged quantities or reconciling incompatible transformations).  

- **Improve Modeller Awareness**: Requires modellers to explicitly define the transformation applied to their target variable, reducing errors caused by mistreatment or misinterpretation of data.  

- **Enhance Model Transparency**: Makes it immediately clear which transformation has been applied, improving auditability for both modellers and reviewers.  

- **Support Automated Validation**: Enables pre-execution checks to verify compliance, ensuring that models adhere to the standard **before they are processed**.  

- **Encourage Thoughtful Target Selection** – Forces modellers to consider whether they have chosen the **most appropriate target transformation** for their model, leading to better-informed modeling decisions. 


**Negative Effects:**
- **Overhead**: requires more work on the part of modellers to ensure that target variables are appropriately named, from the moment they are pulled in from viewser. May require retroactive fixes to querysets and model/ensemble configs.

- **Extra validation required**: An extra piece of validation code will be required to check that the target variables in models and ensembles conform to the rule.

- **Overhead in creating new model types**: Introducing a model with a new type of target variable (e.g. z-transformed) would require introducing a new prefix, updating the validator, and checking parts of the code that rely on the prefix for type detection.



- **Increased Overhead for Modellers**: Requires modellers to ensure that target variables are correctly named **from the moment they are pulled in from viewser**. Existing datasets, querysets, and model/ensemble configurations may need **retroactive updates** to comply with the new standard.

- **Additional Validation Complexity**: A validation mechanism must be implemented to **check compliance before model execution**, adding an extra step in the pipeline.  

- **Manual Updates for New Model Types**: Introducing a model with a **new type of target variable** (e.g., z-transformed) will require:  
  - Defining and **registering a new prefix** in this ODR.  
  - Updating **validation scripts** to recognize the new type.  
  - Reviewing **code dependencies** where transformation type detection affects behavior (e.g., ensemble reconciliation algorithms).  


### Rationale
The primary rationale behind this decision is to guard against silent errors caused by accidental inappropriate treatment of target variables (e.g. logging a variable which is already logged, trying to reconcile a logged variable with a linear or binary variable)

### Considerations
- **Risks**: Not foolproof. There is no fully reliable method to determine if a given list of numbers is, for instance, logged or linear, so even if this convention is enforced, it relies on modellers using it correctly - **there is no way of preventing a linear variable being erroneously named as logged, and the resulting errors may be silent and critical.**

### Additional Notes
None.

### Feedback and Suggestions
Team members and stakeholders are encouraged to provide feedback or suggest improvements on this decision. Please submit any feedback through the project's standard communication channels or by opening an issue in the repository.