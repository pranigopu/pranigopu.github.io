<head>
  <script>
    MathJax = {tex: {inlineMath: [['$', '$']]}};
  </script>
  <script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
  </script>
</head>

[<< Back to **Linear Algebra**](https://pranigopu.github.io/mathematics/linear-algebra)

**SOLVING A SYSTEM OF OF LINEAR EQUATIONS**

---

**Contents**:

- [Introduction](#introduction)
- [Elimination using matrices](#elimination-using-matrices)
  - [Row reduction](#row-reduction)

---

# Introduction
Consider a system of two-dimensional linear equations:

$a_1x + b_1y = c_1$

$a_2x + b_2y = c_2$

Note that here, $x$ and $y$ are two separate variables representing two separate attributes, while the rest are constants. Drawing from [_Geometry of Linear Equations_](https://pranigopu.github.io/mathematics/linear-algebra/geometry-of-linear-equations), we can rewrite the given system using as follows:

$\begin{bmatrix} a_1 \newline a_2 \end{bmatrix} x + \begin{bmatrix} b_1 \newline b_2 \end{bmatrix} y = \begin{bmatrix} c_1 \newline c_2 \end{bmatrix}$

Putting $\vec{a} = \begin{bmatrix} a_1 \newline a_2 \end{bmatrix}$, $\vec{b} = \begin{bmatrix} b_1 \newline b_2 \end{bmatrix}$ and $\vec{c} = \begin{bmatrix} c_1 \newline c_2 \end{bmatrix}$, we get:

$\vec{a} x + \vec{b} y = \vec{c}$

Defining $\vec{x} = \begin{bmatrix} x \newline y \end{bmatrix}$ and $A = \begin{bmatrix} a_1 & b_1 \newline a_2 & b_2 \end{bmatrix}$, we get:

$A \vec{x} = \vec{c}$

---

Now, if $A^{-1}$ exists, then:

$A^{-1} A \vec{x} = A^{-1} \vec{c}$

$\implies I \vec{x} = A^{-1} \vec{c}$

$\implies \vec{x} = A^{-1} \vec{c}$

If $A^{-1}$ is known, $A^{-1} \vec{c}$ can be easily obtained as a 2D vector of constants where each dimension corresponds to a variable (dimension 1 to $x$ and dimension 2 to $y$); hence, we get the solution of the system. Thus, we see that this last reformulation reframes the solving of a system of equations as a process of performing matrix operations, and makes it possible to analyse the system of equations using matrix properties. We shall see more examples soon, but to take one example, we get that a system of equations has a unique (i.e. single) solution if and only if the coefficient matrix $A$ is invertible, which in turn is true if and only if $A$ has a determinant (i.e. if $A$ is non-singular).

The reformulation also hints at a link between reducing the coefficients in system of equations and reducing the rows/columns of the coefficient matrix. We shall prove this hint to be true, and shall use this to systematise the method of solving a system of linear equations using matrix row/column reduction, which can (1) make the process of solving such systems more efficient (cognitively and practically), especially for larger systems, and (2) make it possible to implement system-solving algorithms, which would be valuable in solving large systems that would be cumbersome or even unfeasible for a human to solve.

---

To generalise the reformulation for $n$-dimensional linear equations:

$a_{11}x_1 + a_{12}x_2 ... + a_{1n}x_n = c_1$

$a_{21}x_1 + a_{22}x_2 ... + a_{2n}x_n = c_2$

...

$a_{k1}x_1 + a_{k2}x_2 ... + a_{kn}x_n = c_k$

Putting $A = \begin{bmatrix} a_{11} & a_{12} & ... & a_{1n} \newline ... \newline a_{k1} & a_{k2} & ... & a_{kn} \end{bmatrix}$, $\vec{x} = \begin{bmatrix} x_1 \newline ... \newline x_n \end{bmatrix}$ and $\vec{c} = \begin{bmatrix} c_1 \newline ... \newline c_k\end{bmatrix}$, the system becomes:

$A \vec{x} = \vec{c} \implies \vec{x} = A^{-1} \vec{c}$

# Elimination using matrices
Layout of the topics:

1. Row reduction, i.e. RR (to help eliminate coefficients)
2. Back substitution
3. Elimination matrices, i.e. RR as matrix multiplication
4. Failure conditions for matrix-based elimination

## Row reduction
