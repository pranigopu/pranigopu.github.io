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

[<< Back to **Mathematical Thinking**](https://pranigopu.github.io/mathematics/mathematical-thinking)

**VOCABULARY**

---

**Contents**:

- [Conjunction](#conjunction)
- [Disjunction](#disjunction)
- [Negation](#negation)

---

Here, we shall see the basic language of mathematical thinking.

# Conjunction
The conjuction of two statements $X, Y$ is to join them using "and" in the sense that does not imply order. Hence, we do not use "and" here in any sense that implies order, e.g. we do not use it as used in the statement "I went to the shop and bought some food"; here, the reverse order, i.e. "I bought some food and went to the shop", does not have the same meaning. A conjunction is denoted by $\land$, i.e. $X \land Y$ means $X$ and $Y$. Since we order is irrelevavnt in a conjunction, we have that $X \land Y = Y \land X$, i.e. we have that conjunction is commutative.

---

To define conjunction more precisely:

- If $X$ and $Y$ are both true, then $X \land Y$ is true
- If $X$ or $Y$ or both are false, then $X \land Y$ is false

In other words, $X \land Y$ is true iff both $X$ and Y$ are true.

---

A variety of words can be reduced in logic to a conjunction, such as:

- **"but"**, a conjunction that points to some kind of exception
- **"despite"**, a conjunction that points to some kind of exception to an expectation

# Disjunction
The disjunction of two statements $X, Y$ is to join them using "or" in the inclusive sense and not the exlusive sense. A statement that uses an inclusive "or" to join $X$ and $Y$ allows for either one of $X$ and $Y$ to be true or both to be true, whereas a statement that uses an exclusive "or" to join $X$ and $Y$ allows for one of $X$ and $Y$ to be true but not both; hence an exclusive "or" is an inclusive "or" (as in "one of $X$ and $Y$) along with a conjunction (as in "but not both").

Hence, we see that the inclusive "or" is more fundamental in logic, since (1) it refers to the fundamental quantifier of "some" or "at least one" (i.e. it refers to the case wherein at some or least one of the statements are true), and since (2) an exclusive "or" can be composed using an inclusive "or" (if we also use a conjunction), whereas an inclusive "or" cannot be composed using an exclusive "or". Hence, "or" in mathematics refers only to the inclusive "or". A disjunction is denoted by $\lor$, i.e. $X \lor Y$ means $X$ or $Y$ (inclusive "or"). Since order is irrelevant in a disjunction, we have that $X \lor Y = Y \lor X$, i.e. we have that disjunction is commutative.

---

To define disjunction more precisely:

- If $X$ or $Y$ or both both are true, then $X \land Y$ is true
- If $X$ and $Y$ are both false, then $X \lor Y$ is false

In other words, $X \lor Y$ is true iff at least one of $X$ and Y$ is true.

# Negation
The negation of a statement $X$, denoted by $\lnot X$, is a contradiction of $X$ such that:

- If $X$ is true, $\lnot X$ is false
- If $X$ is false, $\lnot X$ is true

Hence, a negation as defined in logic is a special kind of contradiction; for a statement to be a negation of $X$, it is not sufficient that it merely contradicts $X$, but rather, it must be a statement such that if false, its falsehood is logically contained in the truth of $X$, and if true, its truth is logically contained in the falsehood of $X$. For example, the negation of "I am eating an apple" cannot be "I am eating a banana", since the latter, though contradicting the former, is not a statement whose truth is contained in the falsehood of the former; in other words, the fact that I am not eating an apple does not mean I am eating a banana.

---

Some examples:

- $a \not = b$ means $\lnot (a = b)$