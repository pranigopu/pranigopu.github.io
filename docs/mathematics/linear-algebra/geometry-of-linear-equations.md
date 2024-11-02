<head>
  <script>
    MathJax = {
      tex: {
        inlineMath: [['$', '$']]
      }
    };
  </script>
  <script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
  </script>
</head>

[<< Back to **Linear Algebra**](https://pranigopu.github.io/mathematics/linear-algebra)

**GEOMETRY OF LINEAR EQUATIONS**

---

**Contents**:

- [Introduction](#introduction)

---

# Introduction
Consider a system of two-dimensional linear equations:

$a_1x + b_1y = c_1$

$a_2x + b_2y = c_2$

Note that here, $x$ and $y$ are two separate variables representing two separate attributes, while the rest are constants. Drawing from [_Linear Combinations of Vectors_](https://pranigopu.github.io/mathematics/linear-algebra/linear-combos-of-vectors.html), we can rewrite the given system using vectors such that each vector has two dimensions, one for representing $x$ and one for representing $y$ (in the same order for all the vectors); here, each constant becomes a magnitudes of the dimension corresponding to the variable for which they are a constant multiple. This is given below:

$\begin{bmatrix} a_1 \\ a_2 \end{bmatrix} x + \begin{bmatrix} b_1 \\ b_2 \end{bmatrix} y = \begin{bmatrix} c_1 \\ c_2 \end{bmatrix}$

Putting $\vec{a} = \begin{bmatrix} a_1 \\ a_2 \end{bmatrix}$, $\vec{b} = \begin{bmatrix} b_1 \\ b_2 \end{bmatrix}$ and $\vec{c} = \begin{bmatrix} c_1 \\ c_2 \end{bmatrix}$:

$\vec{a} x + \vec{b} y = \vec{c}$

---

Hence, we get two ways of visualising this system:

**1. Row picture**:

- One equation at a time
- Each equation is a function in a 2D space
- Intersection of the functions is the system's solution

![Row Picture](https://pranigopu.github.io/mathematics/linear-algebra/images/geometry-of-linear-equations--row-picture.png)

**2. Column Picture**:

- One vector at a time
- Each vector is drawn in a 2D space
- $\vec{c}$ is the resultant vector

![Column Picture](https://pranigopu.github.io/mathematics/linear-algebra/images/geometry-of-linear-equations--column-picture.png)