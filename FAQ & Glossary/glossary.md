# Glossary

This glossary provides an overview of the core terms used throughout the VIEWS ML pipeline. It contains definitions of all the relevant terms along with potential VIEWS adapted terminology and explanations.

| Info         | Details               |
|--------------|-----------------------|
| Last updated | 16.07.2025            |
| By author    | Simon, Gemini-2.5-pro |

---

### A Note on Terminology: Model vs. Model Specification

In the context of the VIEWS pipeline, the term **Model Specification** is used to refer to a precise, unique, and reproducible instance of a forecasting workflow. This is more specific than the general term "model" (e.g., Random Forest). A Model Specification is defined by:
- A specific instantiation of a machine learning algorithm.
- A unique, predetermined set of hyperparameters.
- A well-defined set of input features.
- A specific target feature to be forecasted.
- A well-defined range of months that the features span.
- All code and artifacts necessary to generate a comprehensive forecast (e.g., a 36-month forecast for stepshift models).

---

## Table of Contents

- [Key Technical Terms](#key-technical-terms)
- [Key Forecasting Terms](#key-forecasting-terms)
- [Foundational Time-Series Concepts](#foundational-time-series-concepts)
- [Model Forecasting Strategies](#model-forecasting-strategies)
- [Evaluation Strategies and Schemes](#evaluation-strategies-and-schemes)
- [Data and Model Stability Concepts](#data-and-model-stability-concepts)
- [Modeling and MLOps Practices](#modeling-and-mlops-practices)
- [Project-Specific Terminology](#project-specific-terminology)

---

## Key Technical Terms

- **Model Algorithm**: The underlying mathematical approach or class of methods used for a machine learning task (e.g., `Random Forest`, `LSTM`, `XGBoost`). A `Model Specification` is a concrete application of a single model algorithm, chosen and configured to solve a specific problem.
- **Model Artifact**: The serialized file that is the output of a model training process. It contains the learned parameters of the model and can be loaded to make predictions on new data.
- **Configuration Management**: Configuration files specify the settings, hyperparameters, and data sources used to define a **Model Specification**.
- **Hyperparameters**: Parameters that are not learned from the data but are set prior to the training process. They are a key component of a **Model Specification**.
- **Parameters**: The internal variables of a model that are learned directly from the training data. Unlike hyperparameters, parameters are the result of the training process (e.g., the coefficients in a linear regression).
- **Sweep**: An automated process of searching for the optimal set of hyperparameters for a model.
- **Weights & Biases (wandb)**: An MLOps platform used to log information produced during the lifecycle of a **Model Specification**.

---

## Key Forecasting Terms

- **Data Partitioning**: For robust evaluation, historical data is divided into three distinct, chronological partitions. Each partition is a self-contained unit for a specific stage of the modeling lifecycle. The `Calibration` and `Validation` partitions are further subdivided into a `Training Period` and a `Test Period`.
    -   **Calibration Partition**: Used for initial development and hyperparameter tuning.
    -   **Validation Partition**: Used for the final evaluation of candidate model specifications.
    -   **Forecasting Partition**: Used to train the final **Model Specification** for generating true future forecasts.
- **Training Period & Test Period**:
    -   **Training Period**: The specific, continuous block of time (from a start date to an end date) that defines the data used to train a model's parameters. The data within this period is known as the `Training Set`.
    -   **Test Period**: The specific, continuous block of time (from a start date to an end date) that defines the holdout data used for the final evaluation of a trained `Model Specification`. This period must always occur after the `Training Period`. The data within this period is known as the `Test Set`.
- **Training Set**: The actual collection of data points that fall within the `Training Period`.
- **Test Set (Holdout Set)**: The actual collection of data points that fall within the `Test Period`, used to evaluate a model trained on the corresponding `Training Set`.
- **Forecast Origin**: The first time point for which a forecast is generated.
- **Forecast End**: The temporal identifier marking the end of the forecast period.
- **Forecast Horizon**: The total duration or number of steps into the future for which predictions are made.
- **Forecast Step**: The time increment at which predictions are made.
- **Future Covariates vs. Past Covariates**: A critical distinction between types of input features:
    -   **Future Covariates**: Features whose future values are known or can be determined in advance (e.g., calendar-based features like month or year). These can be used safely in any model.
    -   **Past Covariates**: Features whose values are only known for the past and cannot be known in the future (e.g., the value of a stock market index). Using these requires careful handling (e.g., lagging) to avoid data leakage.
- **Panel Data**: A dataset that contains time-series observations for multiple subjects (e.g., countries, grid cells). It has both a time dimension and a cross-sectional dimension.
- **Data Schema**: A formal definition of the structure of a dataset, specifying the names, data types, order, and constraints of each column or feature. It acts as a contract for data moving through the pipeline.
- **Sequence**: An ordered set of data points indexed by time. In time-series forecasting, this is the fundamental data structure for both input features and target variables.
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

- **Forecasting Paradigm**: A classification of forecasting models based on their underlying approach. The two main paradigms are **native time-series models** (e.g., ARIMA) which model temporal dependencies directly, and **reduction-based forecasters**, which adapt standard regression algorithms (e.g., RandomForest) to the forecasting task by using lagged features.
- **Recursive Multi-Step Forecasting**: A single model is trained to predict one step ahead and is used iteratively.
    -   **Common Algorithms**: `Linear Regression`, `Random Forest`, `XGBoost`.
    -   **Notes**: This approach is simple but can suffer from error accumulation, as prediction errors from early steps are propagated into later steps.
- **Direct Multi-Step Forecasting**: A separate model is trained to predict each future time step directly. (Internal name: **stepshifter**).
    -   **Common Algorithms**: `Linear Regression`, `Random Forest`, `XGBoost`.
    -   **Notes**: This approach avoids error accumulation but is computationally expensive.
- **Multi-Output Forecasting**: A single model is trained to predict the entire sequence of future values in one step.
    -   **Common Algorithms**: `Seq2Seq`, `RNNs (LSTM, GRU)`, `Transformers`.
    -   **Notes**: This is often the most sophisticated approach, capable of learning complex relationships between future time steps.
- **Ensemble Forecasting**: A method where predictions from multiple models (**Constituent Models**) are combined to produce a single, often more accurate, final forecast.
- **Constituent Model**: An individual `Model Specification` that serves as a component in an `Ensemble Forecasting` model. Its predictions are one of several that are aggregated to produce the final ensemble forecast.
- **Reconciliation**: A post-processing step to ensure that forecasts that have a hierarchical relationship are coherent. For example, adjusting the forecasts for all PRIO-Grid cells within a country so that their sum equals the independently generated forecast for the country as a whole.

---

## Evaluation Strategies and Schemes

- **Offline vs. Online Evaluation**: A high-level distinction in evaluation philosophy:
    -   **Offline Evaluation**: Evaluating a model's performance on historical data, before deployment. **Backtesting** is the primary method.
    -   **Online Evaluation (Live Evaluation)**: The ongoing process of evaluating a deployed model's performance as new, real-world data becomes available.
- **Backtesting**: The process of evaluating a **Model Specification**'s performance on historical data to simulate how it would have performed in the past.
- **Loss Function vs. Evaluation Metric**: These two terms define what is being measured during model development, and they are not always the same:
    -   **Loss Function**: The function that is directly minimized by the model during the training process. It guides the learning algorithm.
    -   **Evaluation Metric**: The metric that is used to measure the final performance of a model on a holdout test set. This metric is chosen to reflect the actual, real-world performance that is most important for the project.
- **Rolling-Origin Holdout**: A robust backtesting strategy that simulates a real-world forecasting scenario by generating forecasts from multiple, rolling time origins.
- **Metric Aggregation Methods**: Different ways to average performance metrics from a backtest:
    -   **Time-Series-Wise**: Averaging the metric over the entire forecast horizon for each series.
    -   **Step-Wise**: Averaging the metric for each forecast step independently.
    -   **Month-Wise**: Averaging the metric for each calendar month in the test set.
- **Model Contamination**: A form of information leakage where knowledge of the test set's performance inadvertently influences the model selection or training process, leading to an overly optimistic and invalid evaluation. This is a critical failure mode in any backtesting scheme.

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
        -   **Stratified**: The historical average can be calculated over all time points (unstratified) or can be conditioned on specific factors. This stratification can be done across any dimension, such as time (e.g., a specific decade), space (e.g., a specific country or PRIO-Grid cell), or other categorical features (e.g., the country's regime type).
- **Feature Engineering**: The process of creating new input features for a model from the raw data. This can involve creating lagged variables, interaction terms, or other transformations to improve model performance.
- **Backfill**: The process of running a data processing or forecasting pipeline on historical data, for example, to generate features or forecasts for past periods that were not processed at the time.

---

## Project-Specific Terminology

- **Prediction Parallelogram**: A VIEWS-specific term for a data structure that organizes the output of a `Rolling-Origin Holdout` backtest. Each row represents a complete forecast sequence generated from a different forecast origin, creating a parallelogram shape when visualized over time.
- **Strides**: The offset or distance in time between the last data point of the training set and the forecast step being predicted. For a direct forecasting model, a model trained to predict 12 months ahead has a stride of 12.