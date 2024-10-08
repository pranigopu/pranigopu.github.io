[<< Back to **Machine Learning**](https://pranigopu.github.io/machine-learning)

**ESSENTIALS OF ML**

---

**Contents**:

- [Introduction](#introduction)
- [Supervised learning (SL)](#supervised-learning-sl)
- [Unsupervised learning (UL)](#unsupervised-learning-ul)

---

# Introduction
An essential element of ML is recognising the mapping between observations and expectations. ML focuses on three broad kinds of problems based on such mapping: (1) mapping between two variables or sets of variables, (2) mapping a set of things (e.g. entities, events, etc.) to each other or to emergent categories, based on their similarities and differences, and (3) mapping actions to facts that follow (e.g. reactions, responses or effects). Hence, ML is of three broad kinds respectively: (1) supervised learning, (2) unsupervised learning and (3) reinforcement learning.

# Supervised learning (SL)
The goal is to learn the mapping between independent and dependent variables, also called input and output variables respectively (note that ML does not validate the dependence between the variables but takes it for granted). Evidently, the mapping is such that one input value maps to one output value, or one set of inputs values maps to one set of outputs values; hence, the mapping represents a function between inputs and outputs. Examples of such problems are:

| Input(s) | Output(s) |
| --- | --- | --- |
| Wages  | Revenue |
| Image | Label |
| Wind Speed, Cloud Cover, Temperature | Chance of Rain |
| Email | Spam/Not-Spam Tag |
| Song | Genre |

In general, a model us used to estimate the function mapping inputs to outputs, i.e. the target function; the **choice of model** deserves a discussion of its own. Given the model, the target function is estimated by optimising the model's parameters based on some metric of distance between observed and predicted outputs; the **choice of metric** also deserves a discussion of its own.

**NOTE**:

- Inputs are also called features, attributes, predictors or [covariates](https://www.statology.org/covariate/)
- Outputs are also called response variables or target variables
- If output is quantitative, problem is of regression
- If output is categorical, problem is of classification
- If output is ordinal, problem is of ordinal regression
- Metric used to optimise performance is called "objective function"

# Unsupervised learning (UL)
The goal is to relate data points to each other based on observed similarities and differences. Relationships between data points may be implicit or explicit in the ML's output, i.e. the ML has to make decisions based on the observed similarities and differences, and these decisions would at least presuppose, if not posit, the relationships between the data points. Such problems may be of the following broad kinds, which shall be further explored later:

- Clustering (C)
- Association Rule Learning (ARL)
- Dimensionality Reduction (DR)

Examples of such problems:

| Data | Goal | Problem Type |
| --- | --- | --- |
| Images | _Grouping similar images_ | C |
| Customer Purchases | _Relating an item's sale to another's_ | ARL |
| Predictors | _Identify irrelevant predictors_ | DR |