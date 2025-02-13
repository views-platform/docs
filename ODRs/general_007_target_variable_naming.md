# general_007_target_variable_naming ODR

| ODR Info           | Details                                |
|--------------------|----------------------------------------|
| Subject            | Target variable naming                 |
| ODR Number         | 007                                    |
| Status             | proposed                               |
| Category           | general                                |
| Author             | Jim Dale                               |
| Date               | 13/02/2025                             |
| Owner              | [Insert Owner/approver (Name)]         |
| Review Date        | [Insert Last Review Date (DD.MM.YYYY)] |


---

## Context
Although the VIEWS database stores only raw linear features, the VIEWS pipeline is able to accept models with target variables of fundamentally different character, in particular the model may predict the target variable, the logarithm thereof, or a binary representation of the target variable (e.g. where all values > 0 are coded 1, zeros are coded 0 and negative values do not occur or are not permitted), with necessary transformed performed by viewser before the data enters the pipeline. It is likely that other transformations of target variables may be required in the future, e.g. z-transformed or Fourier-transformed.

There is at present no enforced convention indicating whether the target variable has undergone a transform. The need for one is now acute, since the ensemble point-reconciliation algorithm needs to know if the variables it is attempting to reconcile are log or linear, otherwise silent errors will result. Further, reconciliation of some transformed targets may be entirely inappropriate - the reconciliation machinery needs to be table to detect if it is presented with such variables so that it can throw a suitable error.

## Decision
All target variables must have names prefixed with 'xx_', where 'xx' is a two-letter code indicating what type the variable is, and which must be a member of a set of permitted prefixes, which can be added to or amended, but which currently consists of
- 'lr' = 'linear', i.e. untransformed
- 'ln' = 'natural log/log to base e of (1+target), where '1+' allows handling of targets containing zeros
- 'by' = 'binary', constrained to be either 0 or 1

### Overview
---

## Consequences
**Positive Effects:**
Enforcing a rule of this kind will 
- enable all parts of the pipeline to determine/check when necessary what kind of target variable they have been presented with, so that potential silent failures caused by performing inappropriate mathematical operations (e.g. summing logged quantities) can be avoided.
- make the modelling process more transparent to modellers by reminding them what kind of target variable they are dealing with, which will again help avoid errors caused by mistreatment of variables
- force modellers to think about whether they have chosen a suitable target variable for their model

**Negative Effects:**
- **Overhead**: requires more work on the part of modellers to ensure that target variables are appropriately named, from the moment they are pulled in from viewser. May require retroactive fixes to querysets and model/ensemble configs
- **Extra validation required**: An extra piece of validation code will be required to check that the target variables in models and ensembles conform to the rule
- **Overhead in creating new model types**: Introducing a model with a new type of target variable (e.g. z-transformed) would require introducing a new prefix, updating the validator, and checking parts of the code that rely on the prefix for type detection

## Rationale
The primary rationale behind this decision is to guard against silent errors caused by accidental inappropriate treatment of target variables (e.g. logging a variable which is already logged, trying to reconcile a logged variable with a linear or binary variable)

### Considerations
- **Risks**: Not foolproof. There is no fully reliable method to determine if a given list of numbers is logged or linear, so even if this convention is enforced, it relies on models using it correctly - there is no way of preventing a linear variable being erroneously named as logged, and the resulting errors may be silent.

## Additional Notes
None.

## Feedback and Suggestions
Team members and stakeholders are encouraged to provide feedback or suggest improvements on this decision. Please submit any feedback through the project's standard communication channels or by opening an issue in the repository.