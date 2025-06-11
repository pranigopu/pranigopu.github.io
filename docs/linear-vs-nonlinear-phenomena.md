<head>
  <script>
    MathJax = {tex: {inlineMath: [['$', '$']]}};
  </script>
  <script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
  </script>
</head>

<h1>Linear Versus Nonlinear Phenomena</h1>

**_Their relationship, and algorithmic approaches_**

---

**Contents**:

- [Linear Systems](#linear-systems)
- [Nonlinear Systems](#nonlinear-systems)
- [Relevance of Relating Linear and Nonlinear Phenomena](#relevance-of-relating-linear-and-nonlinear-phenomena)
- [Relating Linear and Nonlinear Phenomena](#relating-linear-and-nonlinear-phenomena)
  - [Approximating Nonlinear Systems as Linear Systems](#approximating-nonlinear-systems-as-linear-systems)
    - [Linearisation](#linearisation)
    - [Piecewise Linear Models](#piecewise-linear-models)
  - [Mapping a Nonlinear System to a Linear system](#mapping-a-nonlinear-system-to-a-linear-system)
    - [Principal Component Analysis (PCA)](#principal-component-analysis-pca)
    - [Kernel PCA](#kernel-pca)
- [Conclusion](#conclusion)
- [References](#references)

---

# Linear Systems
Linear systems are systems of inputs and outputs (e.g. causes and effects) wherein (1) the change in the outputs is directly proportional to the change in the inputs, i.e. outputs change at a constant rate with respect to the change in the inputs (homogeneity) and (2) the output of the sum of inputs is the same as the sum of the outputs of individual inputs (superposition). Mathematically, a function $f : X \mapsto Y$ is linear if and only if:

- $f(kx) = kf(x); x \in X, k \in \mathbb{R}$ (homogeneity)
- $f(x_1 + x_2) = f(x_1) + f(x_2); x_1, x_2 \in X$ (superposition)

A linear system's behavior is known as a linear phenomenon. Homogeneity makes the system predictable, since the effect of changes in the inputs on the outputs is predictable and retraceable. Superposition makes the system analysable, since the effect of a sum of inputs can be easily broken down to the sum of the effects of individual inputs. Thus, a linear system is easy to model and analyse, and the identification of real-world systems/phenomena as linear or approximately linear makes the useful properties of linear systems/phenomena applicable to real-world problems, such as forces in equilibrium in structural engineering, Leontief input-output models in economics, electrical circuits (which can be modelled by Ohm's Law, a linear equation), etc.

# Nonlinear Systems
A nonlinear system is simply any system that is not linear, and a nonlinear system's behaviour is known as a nonlinear phenomenon. Nonlinear systems are based on complex interactions between two or more entities, such as interacting effects of two or more factors, feedback loops (i.e. past outputs becoming present inputs) and emergent properties (i.e. properties of a system as a whole that extend beyond the properties of its parts). This complexity leads to complex relationships between inputs and outputs that are, unlike linear systems, (1) not directly proportional and (2) not analysable as a sum of the effects of individual inputs.

These facts can make nonlinear systems much harder to model and analyse than linear systems, making such systems sensitive to initial conditions and/or leading to chaotic outcomes. However, their study is valuable, since a variety of real-world systems/phenomena are nonlinear, such as dynamic fluids, mechanical systems (e.g. $n$-body systems, pendulum/spring oscillations, etc.), natural systems (e.g. climate and ecosystems), economic systems (e.g. financial markets and trade networks), learning/adaptive systems (e.g. deep learning networks and reinforcement learning agents), etc.

# Relevance of Relating Linear and Nonlinear Phenomena
As discussed, many real-world systems/phenomena are nonlinear and must be studied as such. However, nonlinear systems pose several practical and theoretical issues, such as instability (due to sensitivity to initial conditions and chaotic outcomes) and local minima in optimisation problems (due to the mathematical complexity of nonlinear models, which can lead to the optimiser getting stuck in suboptimal solutions). On the other hand, due to homogeneity and superposition, a linear model is far more tractable and interpretable. Hence, it would be valuable to be able to either (1) approximately yet adequately model a nonlinear system as a linear system, or (2) map a nonlinear system to a linear system and thereby apply linear methods in nonlinear contexts.

# Relating Linear and Nonlinear Phenomena
## Approximating Nonlinear Systems as Linear Systems
### Linearisation
Linearisation is the method approximating a nonlinear function $f : X \mapsto Y$ using a linear function $L : X \mapsto Y$ for a given set of input values, i.e. for some $S \subseteq X$. Now, consider the task of approximating a function $f$ around a point $a$. Evidently, the best approximation $L$ would be such that: (1) $L(a) = f(a)$ (i.e. the same output for $a$), (2) $L^1(a) = f^1(a)$ (i.e. the same slope at $a$), (3) $L^2(a) = f^2(a)$ (i.e. the same rate of change of the slope at $a$), etc. In practice, the series has to stop after a finite number of terms. The general form of such an approximation is given by the Taylor series:

$L(x) = f(a) + f^1(a)(x - a) + \frac{f^2(a)(x - a)^2}{2} ... = f(a) + \sum_{k = 1}^{\infty} \frac{f^k(a)(x - a)^k}{k!}$

Now, for a linear approximation, the series must stop at $k = 1$, i.e. $L(x) = f(a) + f^1(a)(x - a)$ (RMIT University, no date); stopping the series at $k > 1$ would result in a nonlinear approximation due to the presence of higher-order terms in $x$ (leading to a quadratic approximation for $k = 2$, a cubic approximation for $k = 3$, etc.). When stopping at $k = 1$, $L$ is an affine function (i.e. a linear function plus a constant) such that (1) $L(a) = f(a)$ and (2) $L$ minimises the error in a local neighborhood around the point $a$, making it the best linear approximation of $f$ in the region around $a$.

### Piecewise Linear Models
A linear approximation of a nonlinear function is always for a region around a point $a$, and thus, the approximation error tends to grow for points further away from $a$. Hence, to use linear approximation while reducing approximation error, the nonlinear function can be approximated by different linear functions for different regions; in other words, the linear approximation can be done piecewise. A key application of piecewise linear modelling is piecewise linear regression, i.e. separate application of linear regression on separate regions of the data.

Now, piecewise linear approximation may result in discontinuity between two regions. Furthermore, when using piecewise linear approximation given only data points (often with some randomness in the data), it is not always clear how to reasonably divide the domain into regions. Nonetheless, piecewise linear modelling can be useful when the inputs and output have roughly linear relationships in some well-defined regions. For example, $c$, the strength of concrete (output) may vary nonlinearly with respect to the $r$, water-to-cement ratio (input), but may be modelled by one roughly linear relationship when $r \leq 70$ and another roughly linear relationship when $r > 70$ (Eberly College of Science, no date).

## Mapping a Nonlinear System to a Linear system
### Principal Component Analysis (PCA)
A phenomenon may be the result of a system where one or more inputs are nonlinearly related to the output. However, if it is possible to identify the contribution (i.e. linear effect) of each input to the output, and if the contribution of inputs linearly related to the output is sufficiently high (i.e. if these inputs explain the output with sufficient accuracy), the inputs causing nonlinearity can be removed, leading to a practically equivalent linear model. To this end, PCA is a method to separate the system's inputs according to their contribution to the output, thereby identifying the "principal components" of the system and opening the way for dimensionality reduction.

### Kernel PCA
However, PCA can only identify linear contributions of inputs to the output, potentially omitting significant nonlinear contributions and thereby mapping the nonlinear system to a practically insufficient and inaccurate linear system. Now, kernel PCA, instead of performing PCA in the original input space (i.e. the set of all possible input combinations), uses kernel methods to map the inputs to a higher-dimensional input space, where linear relationships may exist and thus linear separation using PCA would be more effectively handle nonlinear patterns in the original data (Machine Learning Explained, 2022).

# Conclusion
Linear and nonlinear phenomena are distinct, and by themselves, both have relevance in real-world problems. However, nonlinear phenomena lack the simplicity, predictability, and decomposability of linear phenomena, which is why it is valuable to learn about the complementary relationship linear and nonlinear systems can have. By understanding when and how we can approximate or transform nonlinear systems into linear systems, we greatly improve our ability to address real-world problems in engineering, economics, machine learning, and more. In short, using linear simplicity to tackle nonlinear complexity is a vital strategy in both theoretical research and practical applications.

# References
RMIT University. (no date). _LINEARIZATION USING TAYLOR'S THEOREM AND THE DERIVATION OF SOME COMMON SURVEYING OBSERVATION EQUATIONS_. \[online\] Available from: [https://www.mygeodesy.id.au/documents/Chapter%207.pdf](https://www.mygeodesy.id.au/documents/Chapter%207.pdf).

Harvard University. (no date). 'Unit 11: Linearization', _INTRODUCTION TO CALCULUS_. \[online\] Available from: [https://people.math.harvard.edu/~knill/teaching/math1a2024/handouts/lecture11.pdf](https://people.math.harvard.edu/~knill/teaching/math1a2024/handouts/lecture11.pdf).

Eberly College of Science. (no date).'8.8 - Piecewise Linear Regression Models', _STAT 501 Regression Methods_. \[online\] Available from: [https://online.stat.psu.edu/stat501/lesson/8/8.8](https://online.stat.psu.edu/stat501/lesson/8/8.8).

Machine Learning Explained. (2022). _Kernel PCA_. \[online\] Available from: [https://ml-explained.com/blog/kernel-pca-explained](https://ml-explained.com/blog/kernel-pca-explained).