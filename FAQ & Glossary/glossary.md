# Glossary

This glossary provides an overview of the core terms used throughout the VIEWS ML pipeline. It contains definitions of all the relevant terms along with potential VIEWS adapted terminology and explanations.

| Info         | Details               |
|--------------|-----------------------|
| Last updated | 13.10.2025            |
| By author    | Simon, Gemini-2.5-pro |

---

### Trained Model Object vs. Trainable Model Specification vs. Training Algorithm vs. Family of Training Algorithms

In the context of the VIEWS pipeline, the term model is used to reference many related but nunanced concepts. These can be disambiguited. **Trainable Model Specification** can be used to refer to a precise, unique, and reproducible instance of a forecasting workflow that includes all the information necessary to compute a trained model artifact -- which in turn can produce forecasts if given feature values. This term is more specific than the general term "model" (which could alternatively refer to a Random Forest training algorithm with hyperparameters set to specific values or a training algorithm family like a Random Forest with the hyperparamter values left unspecified). A Computable Model Specification is defined by:
- A training algorithm, which is a specific instantiation of a machine learning or statistical algorithm whose parameters can be trained with data conditional on hyperparameter values being set (but this machine learning algorithm does not include a data specification).
    - A unique, predetermined set of hyperparameter values is part of the training algorithm definition, see below.
    - if one is referring to a family of training algorithms that are similar except for using potentially different hyperparameter values then use training algorithm family/class.
- training data specification rules, which define for any window of true-future or simulated-future target values how to retrieve the necessary data to use the specific training algorithm to produce a trained model artifact. The training data specification rules have two parts:
    - Rules for any window that will produce a well-defined set of input features for the training algorithm.
    - Rules for any window that will provide a target for the training algorithm.
- A window which is a well-defined range of months that the target and features span. This is input to the training data specification so the training data can be returned based on the rules.
- There are other ancillary information that can be associated with a trainable model specification:
    - All code and artifacts necessary to generate a comprehensive forecast (e.g., a 36-month forecast for stepshift models) when the data specification is combined with the training algorithm.
- Note: After a Trainable Model Specification is processed the resulting object that can produce forecasts is a **Trained Model Artifact**.

---

## Table of Contents

- [Key Technical Terms](#key-technical-terms)
- [Key Forecasting Terms](#key-forecasting-terms)
- [Foundational Time-Series Concepts](#foundational-time-series-concepts)
- [Model Forecasting Strategies](#model-forecasting-strategies)
- [Model Terminology](#model-terminology)
- [Evaluation Strategies and Schemes](#evaluation-strategies-and-schemes)
- [Data and Model Stability Concepts](#data-and-model-stability-concepts)
- [Modeling and MLOps Practices](#modeling-and-mlops-practices)
- [Project-Specific Terminology](#project-specific-terminology)

---

## Key Technical Terms

- **Knowledge Graph**: A central data structure that organizes all spatial-temporal information used in the pipeline. It represents entities like countries, grid-cells, and months as nodes, and defines the relationships between them (e.g., `Contains`, `Neighbors`). This graph allows for the precise and flexible querying of data across different resolutions (e.g., finding all grid-cells within a country for a specific year).
- **Training Algorithm Family**: The underlying mathematical approach or class of methods used for a machine learning task (e.g., `Random Forest`, `LSTM`, `XGBoost`).
- **Training Algorithm**: A training algorithm that when given only a data specification defines a unique `Computable Model Specification`. A `Training Algorithm` can belong to a `Training Algorithm Family` but has to also have all necessary specific hyperparameter values assigned to be a `Training Algorithm`.
- **Trainable Model Specification**: A `Trainable Model Specification` is a concrete application of a single model algorithm, chosen and configured to solve a specific task.
- **Trained Model Artifact**: The serialized file that is the output of a model training process. It contains the learned parameters of the model and can be loaded to make predictions on new data.
- **Trainable Model Specification Registry (`M`)**: The central registry or catalog where every `Trainable Model Specification` is stored and assigned a unique identifier (`m`). This registry acts as a dictionary, tracking the `Training Algorithm`, `Data Specification Rules`, and any resulting `Trained Model Artifacts` associated with each specification.
- **Configuration Management**: Configuration files specify the settings, hyperparameters, and data sources used to define a **Trainable Model Specification**.
- **Training process**: The computation of a `trainable model specification` into a `trained model artifact` which can be used for forecasting.
- **Hyperparameters**: Parameters that are not learned from the data during the training process but are set prior to the training process. They are a key component of the **Training Algorithm** within the **Trainable Model Specification**.
- **Parameters**: The internal variables of a model that are learned directly from the training data. Unlike hyperparameters, parameters values can change as a result of the training process (e.g., the coefficients in a linear regression).
- **Sweep**: An automated process of searching for the optimal values of hyperparameters for a set of Computable Model Specifications that often (but do not have to) belong to the same `training algorithm family`. See also Johnny Lawrence.
- **Weights & Biases (wandb)**: An MLOps platform used to log information produced during the lifecycle of a **Trained Model Artifact** and its **Trainable Model Specification**.

---

## Key Forecasting Terms

- **Data Partitioning and Run Types**: For robust evaluation and forecasting, the pipeline is executed in one of three run types, each of which creates specific chronological partitions of the data.
    -   **Calibration Run**: This run type is used for initial model development and hyperparameter tuning (`--sweep`). It creates a **Calibration Partition** of the data, which is subdivided into a `Training Period` and a `Test Period` to train and evaluate initial model versions.
    -   **Validation Run**: To get performance metrics on unseen data, this run type creates a **Validation Partition**. Similar to the calibration run, this partition is split into a `Training Period` and a `Test Period` to train a model and validate its performance.
    -   **Forecasting Run**: This run type is used to generate true future forecasts. It creates a **Forecasting Partition**, which is used to train the final `Trainable Model Specification` on all available historical data before predicting the future.
- **Training Period & Test Period**: These define the time spans used for training and evaluating models.
    -   **Training Period**: The specific, continuous block of time (from a start date to an end date) that defines the data used to train a model\'s parameters. The data within this period is known as the `Training Set`.
    -   **Test Period**: The specific, continuous block of time (from a start date to an end date) that defines the holdout data used for the final evaluation of a trained `Model Specification`. This period must always occur after the `Training Period`. The data within this period is known as the `Test Set`.
- **Training Set**: The actual collection of data points that fall within the `Training Period`.
- **Test Set (Holdout Set)**: The actual collection of data points that fall within the `Test Period`, used to evaluate a model trained on the corresponding `Training Set`.
- **Forecast Origin**: The first time point for which a forecast is generated.
- **Forecast End**: The temporal identifier marking the end of the forecast period.
- **Forecast Horizon**: The total duration or number of steps into the future for which predictions are made.
- **Forecast Step**: The time increment between predictions within a **sequence** of forecasts.
- **Future Covariates vs. Past Covariates**: A critical distinction between types of input features:
    -   **Future Covariates**: Features whose future values are known or can be determined in advance (e.g., calendar-based features like month or year). These can be used safely in any model.
    -   **Past Covariates**: Features whose values are only known for the past and cannot be known in the future (e.g., the value of a stock market index). Using these requires careful handling (e.g., lagging) to avoid data leakage.
- **Panel Data**: A dataset that contains time-series observations for multiple subjects (e.g., countries, grid cells). It has both a time dimension and a cross-sectional dimension.
- **Data Schema**: A formal definition of the structure of a dataset, specifying the names, data types, order, and constraints of each column or feature. It acts as a contract for data moving through the pipeline.
- **Sequence**: An ordered set of data points indexed by time. In time-series forecasting, this is the fundamental data structure for both input features and target variables.
- **Secret**: If you sent me (Simon) a kiwi emoji in a slack channel - preferable one with multiple people in it, I will buy Mike a beer. I\'m just checking if anyone actually reads this.
- **Point Estimate**: A single value that represents the most likely outcome of a forecast.
- **Probabilistic Forecast**: A type of forecast that outputs a full probability distribution for each future time step, rather than a single `Point Estimate`. This allows for the quantification of uncertainty and the generation of `Prediction Intervals`.
- **Prediction Interval**: A range of values within which a future observation is expected to fall with a certain probability.

---

## Foundational Time-Series Concepts

- **Autocorrelation (ACF)**: The correlation between a time series and a lagged version of itself. The Autocorrelation Function (ACF) is a plot of these correlations at different lags and is a key tool for identifying temporal dependencies.
- **Seasonality**: A repeating, predictable pattern in a time series that occurs at regular intervals. It is one of the three classical components of a time series, along with `Trend` and cycle.
- **Trend**: The long-term, underlying direction of a time series. It is one of the three classical components of a time series, along with `Seasonality` and cycle.
- **Non-Stationary Process**: A time series whose statistical properties (e.g., mean, variance) change over time.

---

## Model Forecasting Strategies

- **Forecasting Paradigm**: A classification of forecasting algorithm families based on their underlying approach. The two main paradigms are **native time-series models** (e.g., ARIMA) which model temporal dependencies directly, and **reduction-based forecasters**, which adapt standard regression algorithms (e.g., RandomForest) to the forecasting task by using lagged features.
- **Recursive Multi-Step Forecasting**: A single one-step-ahead-trainable model specification is created and trained to predict one step ahead targets only and the resulting one-step-ahead trained model artifact is used iteratively to produce multiple forecasts from the steps from 1 to the maximum forecast horizon.
    -   **Common Algorithms**: `Linear Regression`, `Random Forest`, `XGBoost`.
    -   **Notes**: This approach is simple but can suffer from error accumulation, as prediction errors from early steps are propagated into later steps. For consistency, Recursive Multi-Step Forecasting also entails the organization of a overall-trainable model specification from the one-step-ahead-trainable model specification and the composition of a overall-trained model artifact from the one-step-ahead-trained model artifact to make forecasts for the window from 1 to the maximum horizon steps into the future.
- **Direct Multi-Step Forecasting**: A set of separate step-specific-trainable model specifications are each trained to predict each future time step directly from 1 to the maximum forecast horizon. (Internal name: **stepshifter**).
    -   **Common Algorithms**: `Linear Regression`, `Random Forest`, `XGBoost`.
    -   **Notes**: This approach can avoid error accumulation but is more computationally expensive. For consistency, Direct Multi-Step Forecasting also entails the organization of a overall-trainable model specification from the step-specific-trainable model specifications and the composition of a overall-trained model artifact from the step-specific-trained model artifacts to make forecasts for the window from 1 to the maximum horizon steps into the future.
- **Multi-Output Forecasting**: A single trainable model specification is trained to predict the entire sequence of future values from 1 to the maximum horizon in one operation.
    -   **Common Algorithms**: `Seq2Seq`, `RNNs (LSTM, GRU)`, `Transformers`.
    -   **Notes**: This is often the most principled approach, capable of learning complex relationships between future time steps. However, there are computational costs and evaluation of the cross-time-step information is more complicated.
- **Ensemble Forecasting**: A method where predictions from multiple trainable model specifications (**Constituent Models**), each that can produce forecasts for the steps 1 to the maximum horizon in a forecasting window, are combined to produce a single, often more accurate, final forecast.
- **Constituent Model**: An individual `Trainable Model Specification` that serves as a component in an `Ensemble Forecasting` model. Its predictions are one of several that are aggregated to produce the final ensemble forecast.
- **Reconciliation of forecasts across resolutions**: A post-processing step to ensure that forecasts that have a hierarchical relationship acros spatial-temporal resolutions are coherent. For example, adjusting the forecasts for all PRIO-Grid cell-months within a country-month so that the sum of those PRIO-Grid cell-month expected values equals the independently generated forecast for the expected value (fo the sum) for that country as a whole.

---

## Model Terminology
**Model Naming Convention:** The models belonging to the VIEWS pipeline follow a predetermined naming standard, which takes the form of `adjective_noun`, for example *brown_cheese*.

The models are collected in the [model catalog](https://github.com/views-platform/views-models?tab=readme-ov-file#catalogs), with the following properties:
- **Model Name:** adjective_noun
- **Algorithm:** 
- **Targets:** e.g. ln_ged_sb_dep
- **Input Features:** name and link to the used queryset
- **Non-default Hyperparameters:** name and link to the hyperparameters
- **Forecasting Type:** TBD
- **Implementation Status:** deployed, shadow, deprecated
- **Implementation Date:** 
- **Author:** creator of the model

---

## Evaluation Strategies and Schemes

- **Offline vs. Online Evaluation**: A high-level distinction in evaluation philosophy:
    -   **Offline Evaluation**: Evaluating a model\'s performance on historical data, before deployment. **Backtesting** is the primary method.
    -   **Online Evaluation (Live Evaluation)**: The ongoing process of evaluating a deployed model\'s performance as new, real-world data becomes available.
- **Backtesting**: The process of evaluating a **Model Specification**\'s performance on historical data to simulate how it would have performed in the past.
- **Loss Function vs. Evaluation Metric**: These two terms define what is being measured during model development, and they are not always the same:
    -   **Loss Function**: The function that is directly minimized by the model during the training process. It guides the learning algorithm.
    -   **Evaluation Metric**: The metric that is used to measure the final performance of a model on a holdout test set. This metric is chosen to reflect the actual, real-world performance that is most important for the project.
- **Rolling-Origin, Fixed Length Holdout**: A robust backtesting strategy that simulates a real-world forecasting scenario by generating forecasts for 1 to the maximum horizon time steps that each originate from a sequence of multiple, rolling simulated futures/origins.
- **Metric Aggregation Methods**: Different ways to average performance metrics from a backtest:
    -   **Time-Series-Wise**: Averaging the metric over the entire forecast horizon for each series. There are $W$ values of the metric calculated where $W$ is the number of windows.
    -   **Step-Wise**: Averaging the metric for each forecast step independently. There are then the same number of values for this metric as the maximum horizon step, $H^{(max)}$.
    -   **Month-Wise**: Averaging the metric for each calendar month in the test set. Here there are $W+H^{(max)}$ values for the metric.
    -   **Equal-time-slice-weighted-Time-Series-Wise**: A weighted average is taken over all of the scores within each of the windows so that the resulting average across all the windows includes an equal amount of information for each time-slice. Without this weighting, the first and last time slices in a sequence of rolling-windows are given less weight because they only appear in a limited number of the windows (1 for the first and last time slice, 2 for the second and second-to last time slice, etc up to a maximum of 12 counting from either end). The weight applied is simply $\frac{1}{C^{(t)}_{\{1:W\}}}$ where $C^{(t)}_{1:W}$ is the count of how many of the $W$ window time slice $t$ falls within.
- **Model Contamination**: A form of information leakage where knowledge of the test set\'s performance inadvertently influences the model selection or training process, leading to an overly optimistic and invalid evaluation. This is a critical failure mode in any backtesting scheme.

---

## Data and Model Stability Concepts

- **Domain Shift**: A general term for any situation where the data distribution of the environment where the model is deployed (the "target domain") is different from the data distribution of the environment where the model was trained (the "source domain"). The two most important types are:
    -   **Data Drift**: Occurs when the distribution of the **input features** changes.
    -   **Concept Drift**: Occurs when the relationship between the **input features and the target variable** changes.
- **Drift Detection**: The process of using statistical tests or monitoring techniques to identify `Domain Shift`, signaling that a model may need to be retrained or that there is an issue with the data pipeline.

---

## Modeling and MLOps Practices

- **Baseline Model**: A simple, often naive, forecasting model that serves as a reference point for evaluating more complex models. A complex model is only useful if it performs better than a simple baseline. Common examples include:
    -   **No-Change / Persistence Model**: Predicts that the future value will be the same as the last observed value.
    -   **Zero Model**: Always predicts zero for all future steps.
    -   **Conflictology Model**: Predicts the historical, long-term average value for a given location and time of year (analogous to a climatology forecast in weather prediction). This baseline can have several variations:
        -   **Deterministic vs. Probabilistic**: A deterministic version predicts a single number (the historical mean), while a probabilistic version provides a full distribution of historical outcomes from which samples can be drawn.
        -   **Stratified**: The historical average can be calculated over all time points (unstratified) or can be conditioned on specific factors. This stratification can be done across any dimension, such as time (e.g., a specific decade), space (e.g., a specific country or PRIO-Grid cell), or other categorical features (e.g., the country\'s regime type).
- **Feature Engineering**: The process of creating new input features for a model from the raw data. This can involve creating lagged variables, interaction terms, or other transformations to improve model performance.
- **Backfill**: The process of running a data processing or forecasting pipeline on historical data, for example, to generate features or forecasts for past periods that were not processed at the time.

---

## Project-Specific Terminology

- **Prediction Parallelogram**: A VIEWS-specific term for a data structure that organizes the output of a `Rolling-Origin Holdout` backtest. Each row represents a complete forecast sequence generated from a different forecast origin, creating a parallelogram shape when visualized over time.

---

## Things to Clarify

1.  The new glossary introduces a precise hierarchy: `Training Algorithm Family`, `Training Algorithm`, `Trainable Model Specification`, and `Trained Model Artifact`. How is this conceptual distinction practically implemented in the project\'s repository structure and code? For instance, is there a specific directory for `Trainable Model Specifications`?

2.  The new `Equal-time-slice-weighted-Time-Series-Wise` metric for backtesting is quite specific. What was the issue with the simpler aggregation methods that prompted the creation of this more complex, weighted approach?

3.  The document notes that `Direct Multi-Step` and `Multi-Output` forecasting are computationally expensive. What are the practical guidelines used to decide when the potential performance gain from these methods justifies their higher cost compared to the simpler `Recursive` strategy?

4.  The terms `Trainable Model Specification` and `Computable Model Specification` are used across both the glossary and the formal notation document, but the exact distinction is unclear. This ambiguity should be resolved to either use a single, consistent term or to explicitly define the difference between them.

5.  The definition for `Sweep` includes the note "See also Johnny Lawrence." What is the context or inside joke behind this reference?
