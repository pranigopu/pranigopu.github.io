**LOGIC**

---

The following document was a result of notes taken and expanded from the following lecture series (on YouTube) by Dr. Leonard Peikoff:

- [Introduction to Logic by Leonard Peikoff](https://www.google.com/url?q=https://www.youtube.com/playlist?list%3DPLqsoWxJ-qmMtr7i6D_yvSpPC-hTOzdWas&sa=D&source=editors&ust=1719669354663441&usg=AOvVaw3FJcSXykA2ogsleOpH3Sv-)
- [Induction in Physics and Philosophy by Leonard Peikoff](https://www.google.com/url?q=https://youtube.com/playlist?list%3DPLqsoWxJ-qmMvgyTXdOjsdszOZ3ppFJAnp%26si%3DCBp8LQ3ca_5p-4q9&sa=D&source=editors&ust=1719669354663941&usg=AOvVaw20S43fBwMi8uA0O0ParHDl)

---

TABLE OF CONTENTS

- [Need for cognitive validating standard](#need-for-cognitive-validating-standard)
  - [Metaphysical validation](#metaphysical-validation)
  - [Epistemological validation](#epistemological-validation)
- [Forming cognitive validating standard](#forming-cognitive-validating-standard)
  - [Introduction](#introduction)
  - [The laws of logic](#the-laws-of-logic)
    - [The law of identity](#the-law-of-identity)
    - [The law of noncontradiction](#the-law-of-noncontradiction)
    - [The law of excluded middle](#the-law-of-excluded-middle)
  - [Validation by reaffirmation through denial](#validation-by-reaffirmation-through-denial)
  - [Aspects of logical proof](#aspects-of-logical-proof)
- [Informal fallacies](#informal-fallacies)
  - [Classification by nature of distortion](#classification-by-nature-of-distortion)
  - [Classification by essential error](#classification-by-essential-error)
- [Deductive reasoning](#deductive-reasoning)
  - [Introduction](#introduction-1)
  - [Types of logical statements](#types-of-logical-statements)
    - [Hypothetical statement](#hypothetical-statement)
      - [Form](#form)
      - [Symbolic representations](#symbolic-representations)
      - [Terminology](#terminology)
      - [Relationships](#relationships)
        - [For “if P then Q”](#for-if-p-then-q)
        - [For “only if P then Q”](#for-only-if-p-then-q)
      - [Translating from informal to formal](#translating-from-informal-to-formal)
    - [Alternative statement](#alternative-statement)
      - [Form](#form-1)
      - [Symbolic representations](#symbolic-representations-1)
      - [Terminology](#terminology-1)
    - [Categorical statement](#categorical-statement)
      - [Form](#form-2)
      - [Terminology](#terminology-2)
        - [Terms of a statement](#terms-of-a-statement)
        - [Subject](#subject)
        - [Predicate](#predicate)
        - [Quantity](#quantity)
        - [Quality](#quality)
        - [Parts and types of categorical statements](#parts-and-types-of-categorical-statements)
      - [Notes for conceptual clarity of above terms](#notes-for-conceptual-clarity-of-above-terms)
        - [Quantity](#quantity-1)
          - [Synonymous quantities](#synonymous-quantities)
          - [Always default to particular](#always-default-to-particular)
          - [True for all implies true for some](#true-for-all-implies-true-for-some)
          - [“Some” is not “only some”](#some-is-not-only-some)
        - [Quality](#quality-1)
          - [Quality as logical not semantic or grammatical](#quality-as-logical-not-semantic-or-grammatical)
        - [Subject \& predicate](#subject--predicate)
          - [Defining subject \& predicate as classes](#defining-subject--predicate-as-classes)
          - [Specified parts of a whole as subjects](#specified-parts-of-a-whole-as-subjects)
      - [Analysis of propositions](#analysis-of-propositions)
        - [Categorisation based on quantity \& quality](#categorisation-based-on-quantity--quality)
        - [Distribution](#distribution)
        - [Analysing propositions by distributivity of terms](#analysing-propositions-by-distributivity-of-terms)
          - [A-proposition](#a-proposition)
          - [E-proposition](#e-proposition)
          - [I-proposition](#i-proposition)
          - [O-proposition](#o-proposition)
          - [Summary](#summary)
      - [Relationships](#relationships-1)
        - [For “only S is P”](#for-only-s-is-p)
        - [For different proposition types with identical subjects \& predicates](#for-different-proposition-types-with-identical-subjects--predicates)
          - [Contradictory](#contradictory)
          - [Contrary](#contrary)
          - [Subcontrary](#subcontrary)
          - [Superimplication \& subimplication](#superimplication-subimplication)
          - [Summary](#summary-1)
      - [Translating from informal to formal](#translating-from-informal-to-formal-1)
  - [Types of arguments and their validations](#types-of-arguments-and-their-validations)
    - [Mixed hypothetical argument](#mixed-hypothetical-argument)
      - [Forms of such an argument](#forms-of-such-an-argument)
        - [Affirming the antecedent](#affirming-the-antecedent)
        - [Denying the antecedent](#denying-the-antecedent)
        - [Affirming the consequent](#affirming-the-consequent)
        - [Denying the consequent](#denying-the-consequent)
        - [Summary](#summary-2)
    - [Pure hypothetical argument](#pure-hypothetical-argument)
      - [Rules for a valid chain](#rules-for-a-valid-chain)
      - [Translating from informal to formal](#translating-from-informal-to-formal-2)
    - [Alternative argument](#alternative-argument)
      - [Weak alternative](#weak-alternative)
      - [Strong alternative](#strong-alternative)
    - [Categorical argument](#categorical-argument)
      - [Immediate inference](#immediate-inference)
        - [Obversion](#obversion)
        - [Conversion](#conversion)
        - [Contraposition](#contraposition)
      - [Syllogism](#syllogism)
        - [Terminology](#terminology-3)
        - [5 rules of syllogistic validity](#5-rules-of-syllogistic-validity)
          - [Rule 1: Three terms rule](#rule-1-three-termsrule)
          - [Rule 2: Distributive middle rule](#rule-2-distributive-middle-rule)
          - [Rule 3: Matching distributivity rule](#rule-3-matching-distributivity-rule)
          - [Rule 4: Two negative premises rule](#rule-4-two-negative-premises-rule)
          - [Rule 5: Negative statement rule](#rule-5-negative-statement-rule)
        - [Validating syllogisms in practice](#validating-syllogisms-in-practice)
        - [General steps for solving a syllogistic argument](#general-steps-for-solving-a-syllogistic-argument)
    - [Translating from informal to formal–general points](#translating-from-informal-to-formalgeneral-points)
- [Definition](#definition)
  - [Introduction](#introduction-2)
  - [Elaboration of key terms](#elaboration-of-key-terms)
    - [Unit](#unit)
    - [Concept](#concept)
      - [Measurement omission](#measurement-omission)
      - [Concept vs. conceptualisation](#concept-vs-conceptualisation)
    - [Essential vs. fundamental](#essential-vs-fundamental)
      - [Essential](#essential)
      - [Fundamental](#fundamental)
    - [Essential and fundamental characteristics](#essential-and-fundamental-characteristics)
      - [Key terms](#key-terms)
      - [Objectivity of essentials \& fundamentals](#objectivity-of-essentials--fundamentals)
    - [Definition](#definition-1)
      - [Elaborating on the purpose of definitions](#elaborating-on-the-purpose-of-definitions)
      - [Definition vs. concept](#definition-vs-concept)
      - [What a definition is not](#what-a-definition-is-not)
        - [Ostensive definition](#ostensive-definition)
        - [Etymological report](#etymological-report)
  - [Rules for valid definitions](#rules-for-valid-definitions)
    - [Rule 1: Structure rule](#rule-1-structure-rule)
      - [Validation](#validation)
        - [Basic validation](#basic-validation)
        - [Validation using CCD](#validation-using-ccd)
        - [Combining both validations](#combining-both-validations)
      - [Hierarchy of genera](#hierarchy-of-genera)
      - [Specificity of genera](#specificity-of-genera)
        - [How context determines specificity](#how-context-determines-specificity)
    - [Rule 2: Equivalence rule](#rule-2-equivalence-rule)
      - [Validation](#validation-1)
      - [Fallacies due to violation](#fallacies-due-to-violation)
      - [Checking equivalence](#checking-equivalence)
    - [Rule 3: Fundamentality rule](#rule-3-fundamentality-rule)
      - [Validation](#validation-2)
      - [Identifying the fundamentals](#identifying-the-fundamentals)
      - [Fundamentality being the fundamental rule](#fundamentality-being-the-fundamental-rule)
    - [Rule 4: Circularity rule](#rule-4-circularity-rule)
      - [Ways to commit circularity](#ways-to-commit-circularity)
        - [Defining correlatives](#defining-correlatives)
    - [Rule 5: Negatives rule](#rule-5negatives-rule)
      - [Validation](#validation-3)
    - [Rule 6: Obscurity rule](#rule-6-obscurity-rule)
      - [Validation](#validation-4)
    - [Final notes on validating definitions](#final-notes-on-validating-definitions)
  - [Tips for forming valid definitions](#tips-for-forming-valid-definitions)
- [Inductive reasoning](#inductive-reasoning)
  - [Definitions \& clarifications](#definitions--clarifications)
    - [Generalisation](#generalisation)
    - [Concept vs. generalisation](#concept-vs-generalisation)
  - [The problem of induction](#the-problem-of-induction)
  - [Axioms of induction](#axioms-of-induction)

---

# Need for cognitive validating standard
## Metaphysical validation
_The primacy of existence_...

Existence exists and that which exists is what it is, independent of consciousness. In other terms, facts have primacy and are independent of consciousness.

## Epistemological validation
Human consciousness is volitional, i.e. capable of selective focus of awareness of its contents. Beyond the basic sensations and perceptions, the contents–the conceptual contents–of such a consciousness do not automatically conform to reality. Therefore, human cognition needs a valid method of cognition to ensure that its contents correspond to reality.

Logic is the validating standard of cognition, i.e. the method by which a volitional, non-automatic consciousness can ensure that its contents correspond to reality.

# Forming cognitive validating standard
## Introduction
The purpose of a validating standard–logic–is to enable us to determine when our conclusions do or do not represent facts. Only a standard derived from facts, i.e. from reality can enable us to perform this function; an arbitrary standard detached from facts is detached from reality, and thus, deals with the non-existent.

In particular, since we need a standard that is applicable to any and every fact, i.e. a standard that is universally applicable to facts, we need principles inherent in the nature of facts, i.e. inherent in the nature of existence as such. In other terms, we need principles that are true of being qua being.

## The laws of logic
(Credits to: Aristotle)

| Symbol | Meaning |
| --- | --- |
| A, B, C... | any part of reality |
| ¬   | “not” |

### The law of identity
Logic has but one law, which is the basic metaphysical axiom inherent in being qua being, namely the law of identity:

**A is A**

The subsequent laws of logic are, in essence, restatements of the law of identity. However, they are restatements for epistemological use, i.e. for the purpose of finding out whether the law of identity has been violated or not.

### The law of noncontradiction
A cannot be ¬A in the same respect.

The term “respect” means, “in relation to certain facts,” i.e. “in a certain context.” Note that with time, A can become ¬A in the same respect– but at that point, it is A anymore. Hence, to specify “at the same time” is redundant here.

Note that A and ¬A here can be anything; an entity, an attribute, an action, a unit, a concept, etc. Also, A and ¬A need not be the same kind of existent. With this in mind, we can apply the law of noncontradiction to the attribution of information.

---

if

A is B at a given time and in a given respect,

then

A cannot be ¬B at the same time & in the same respect.

---

To combine the above into one statement:

**A cannot be B and ¬B at the same time & in the same respect**

Note that time is emphasised because though it is implied in the term “respect”, it is the most important factor to consider.

### The law of excluded middle
**Anything that exists is either A or ¬A at a given time & in a given respect**

This is validated by the law of noncontradiction. Note that the law of excluded middle does not state that A is either B or ¬B, where B is some particular. Assuming A and B are not identical, ¬A includes B but extends beyond it to include everything except A.

## Validation by reaffirmation through denial
The laws of logic, i.e. the law of identity and its corollaries, are presupposed by any and every thought and action. To deny these laws presupposes their validity, i.e. any supposed refutation of these laws is forced to count on their validity, thereby becoming self-refuting. For example, the concept of “proof” rests on the validity of logic; you cannot “prove” the validity of logic, as logic is what makes proof possible. However, this very fact validates logic, i.e. shows that logic corresponds to reality.

Validation is the process of showing that an idea corresponds to reality. Proof is a kind of validation that uses logic.

## Aspects of logical proof

A logical argument is composed of 3 broad parts:

1.  Premise–known facts or assertions
2.  Inference–application of the laws of logic
3.  Conclusion–fact necessitated by the premise and inference

For a conclusion to be true, (1) the premises must be true, and (2) the inference must be valid, i.e. must be based only on the laws of logic. Premises are validated by valid concept-formation, which in turn is validated by a hierarchy of valid abstractions based on the material provided by sense-perception. Showing that a conclusion follows logically from true premises is proof. Note that the opposite of proof is arbitrary, which is baseless and disintegrated from knowledge.

Hence, the application of logic presupposes a valid method of concept-formation. The elaboration of such a method belongs to the broader domain of epistemology, and shall not be discussed in-depth here (it is discussed to some extent in the chapter "Definitions").

# Informal fallacies

A fallacy is an invalid form of reasoning, i.e. a form of reasoning that does not in fact prove the conclusion it claims to be proving. Common or informal fallacies are fallacies that apply to any form of reasoning, be it deductive or inductive. Being aware of key fallacies enables you to

- Spot invalid arguments–plausible or not–with more ease.
- Identify the nature of the flaw in the invalid argument.

In other words, being aware of fallacies is a means of intellectual self-defence.

## Classification by nature of distortion
1. Distorting the nature of truth
    1. Truth as second-handed
        1. Argumentum ad verecundiam, i.e. Appeal to reverence
        2. Argumentum ad hominem, i.e. Appeal to the man
            1. Abusive
            2. Circumstantial
        3. Cliche thinking
    2. Truth as irrelevant
        1. Argumentum ad baculum, i.e. Appeal to the stick
        2. Appeal to laughter
    3. Truth as subjective
        1. Appeal to personal emotion
        2. Argumentum ad populum, i.e. Appeal to others’ emotion
        3. Argumentum ad misericordiam, i.e. Appeal to pity
    4. Truth as arbitrary
        1. Argumentum ad ignorantiam, i.e. Appeal to ignorance
            1. Agnostic fallacy
2. Distorting the context
    1. Unresolved ambiguity
        1. Equivocation
        2. Amphiboly
        3. Accent
            1. Excerpt-lifting
    2. Neglecting necessary context
        1. Presupposing the conclusion, i.e. Petitio principii, i.e. Begging the question
            1. Restatement
            2. Arbitrary definition
            3. Circular reasoning
            4. Question begging epithet
        2. Denying the presuppositions, i.e. Stolen concept fallacy
        3. Ignoring aspects of composition
            1. Composition fallacy
            2. Division fallacy
        4. False alternative fallacy
    3. Taking invalid presuppositions for granted
        1. Loaded/complex/leading question
        2. Misuse of the mean
        3. Double standard
            1. Special pleading

---

_Now for some definitions_...

**Truth**:

A cognitive element that identifies a part of reality.

**Context**:

The sum of cognitive elements in relation to which any item of human knowledge is acquired, validated or applied.

---

Note, hence, that distorting the truth involves distorting the context, and vice versa. The classification, however, is based on what distortion was primary: the explicit elements of the argument or the implicit elements not stated yet underlying the argument.

## Classification by essential error
A logical argument falls short either due to the inclusion of irrelevant elements or due to the exclusion of relevant ones. Hence, in essence, any fallacy is a combination of one or both of the following fallacies:

1.  Irrelevant conclusion (ignoratio elenchi, i.e. ignoring refutation)
    1.  Straw man fallacy
    2.  Extension
2.  Neglected aspect

In practice, due to the overarching nature of these fallacies, apply them only when none of the other more specific kinds apply.

# Deductive reasoning
## Introduction
Deductive reasoning is a form of reasoning that makes explicit a conclusion implicit, i.e. contained, in the given premises. Hence, deduction is the process of reasoning from universals to either universals or particulars. By its nature, at least one of a deduction’s premises are at least as wide in extent as the conclusion.

---

**Example of a wide conclusion**:

- All men are rational.
- All rational beings have volition.
- ∴ All men have volition.

**Example of a narrow conclusion**:

- All cats are mortal.
- Socrates is a cat.
- ∴ Socrates has mortal.

---

In other terms, a conclusion of a deduction is no wider in extent than at least one of the premises.

## Types of logical statements
### Hypothetical statement
#### Form
**if P then Q**

Note that the order of terms is crucial.

#### Symbolic representations
If P then Q:

- P → Q
- P ⟹ Q
- P > Q
- P ⊃ Q

#### Terminology
- **Antecedent**: P (the condition)
- **Consequent**: Q (conditional conclusion)

#### Relationships
##### For “if P then Q”
if P then Q, i.e. P → Q, implies:

- If P happens, Q _necessarily_ follows.
    - **∴ P is a sufficient condition for Q.**
- If Q happens, P may or may not happen.
    - _Nothing in the statement P→Q makes P necessary for Q._
    - But, as Q necessarily follows P, ¬Q _necessarily_ implies ¬P.
    - **∴ ¬Q is a sufficient condition for ¬P.**

Hence, we have that

P → Q ≡ ¬Q → ¬P

##### For “only if P then Q”
only if P then Q, implies:

- P is a necessary condition for Q.
    - **∴ if Q happens, P necessarily follows.**

Hence, we have that

only if P then Q ≡ Q → P ≡ ¬P → ¬Q

#### Translating from informal to formal
The essence of any conditional or hypothetical word or phrase is captured by the form “if… then…” For example:

- unless P, Q = if not P then Q
- only if P then Q =
    - if Q then P
    - if not P then not Q

### Alternative statement
#### Form
**A or B**

Note that this may be strong alternation (i.e. either A or B but not both) or weak alternation (i.e. either A or B or both). Also, note that unlike a hypothetical, the order of terms is irrelevant. Note:

- **Weak alternative**: Either one is true, but both can be true
- **Strong alternative**: Either one is true, but both cannot be true

#### Symbolic representations
- A ∨ B (weak alternative)
- A ⊻ B (strong alternative)

#### Terminology

- **Alternant**: One of the alternatives offered
- **Weak alternative**: Either one is true, but both can be true.
- **Strong alternative**: Either one is true, but both cannot be true.

### Categorical statement
#### Form
q S c P

Here:

- q is the quantifier
- c is the copula
- S is the subject class
- P is the predicate class

#### Terminology
A categorical statement is classified with respect to 2 measures:

- **Quantity**
- **Quality**

##### Terms of a statement
A term of a statement is any class present in the statement. Hence, “term” may refer to either the subject or the predicate.

##### Subject
The subject is the being or class of beings about which the statement gives some information. In other words, the subject is what the statement talks about.

##### Predicate
The predicate is the being or class of beings to which the subject is related. Through this relationship, the predicate conveys some information about the subject. In other words, the predicate is what the statement says about the subject.

##### Quantity
Denotes either universality or particularity for a class, i.e. refers to either all members or some members of a class. The quantifier denotes the quantity of the subject class.
##### Quality

Denotes either an affirmative (“is”) or a negative (“is not”)  relationship between the subject and the predicate. The copula denotes the quality of the statement.

##### Parts and types of categorical statements
_Overview of the parts of a categorical statement_...

| Part | Name | Form | Function |
| --- | --- | --- | --- |
| 1   | Quantifier | “all” or “some” | Denotes quantity. |
| 2   | Subject |     |     |
| 3   | Copula | “be” or “not to be” | Denotes quality. |
| 4   | Predicate |     | Informs about the subject. |

---

_Overview of the types of categorical statements_...

| Type | Name | Form |
| --- | --- | --- |
| A-proposition | Universal affirmative | All S is P |
| E-proposition | Universal negative | No S is P |
| I-proposition | Particular affirmative | Some S is P |
| O-proposition | Particular negative | Some S is not P |

#### Notes for conceptual clarity of above terms
##### Quantity
###### Synonymous quantities
In ordinary language, there are many ways to refer to universality or particularity. For instance, universality may be unstated yet implied, or stated as “every”, “any”, “whichever”, etc. while particularity may be unstated yet implied, or stated as “many”, “most”, “a number of”, etc. Whatever the word or expression, universality is always translated as “all” and particularity is always translated as “some”.

###### Always default to particular
When it is not clear whether a statement denotes a universal or particular quantity, default to the particular “some”, since it commits you to the minimum information, thus removing the chance of making wrong assumptions.

###### True for all implies true for some
A universal statement implies a particular one. For example, we know that since all men are mortal, then some men are mortal. In other terms, if something is true for all, then it is true for some.

###### “Some” is not “only some”
Without extra knowledge (beyond the given statements), a statement with a particular quantity (i.e. an I or O-proposition) does not inform us about the rest of the subject class. For example, "some men are liars" does not mean that the rest of the men are not. The rest of the men may be honest, or they may be liars as well. However, this statement alone does not inform us about it. However, when we say "only some men are liars", then we know that the rest of the men are not liars. An “only some” statement tells us about the rest of the class, while a “some” statement by itself does not.

---

**Why “only some” is not a valid quantifier**:

“Only some” is not an essential quantifier, since an “only some” statement is reducible to two “some” statements. More precisely, an “only some” statement is reducible to one I-proposition + one O-proposition having the same predicate. Hence, "only some men are liars" is equivalent to "some men are liars" + "some men are not liars".

Note that a set of statements such as the one above is sufficient to inform us about all the rest of the men, since the law of excluded middle means something is either A or ¬A, but cannot be both. Hence, we know that some men are liars and all the rest of them are not, since there is no category between “liar” and “non-liar”.

##### Quality
###### Quality as logical not semantic or grammatical
The affirmative or negative denoted copula is not in terms of the semantic or grammatical relationship but logical relationship. For example,

- "Some men are unintelligent" = Affirmative
- "Some men are not intelligent" = Negative

##### Subject & predicate
###### Defining subject & predicate as classes
In any statement, ordinary or logical, there is always a subject, i.e. what the statement talks about, and a predicate, i.e. what the statement says about the subject. In any case, the subject and predicate can always be defined as classes. This is easy to see for a subject. Now, a predicate assigns some information to the members of the subject class; this is equivalent to relating to the subject class a class of beings to whom that information applies.

###### Specified parts of a whole as subjects
**Singular subjects**:

When the subject consists of a single entity, such as “Socrates” or “Athens”, we apply the universal quantifier “all”. Hence, we get: "all Socrates" and "all Athens". The reason is that a single entity is a unified whole. If it is irreducible, then it consists only of itself. If not, then it consists of its components. In either case, the universal quantity is the right quantity to apply to singular subjects.

---

**Specified parts of a larger class as subjects**:

In cases where you consider a part or subset of some larger class while not considering the larger class in the argument, the part or subset is the subject class, and universal or particular quantifiers would apply to it.

---

Note that in both cases, you are considering the subject of the statement as a whole rather than a part of a whole, at least logically if not semantically. In other words, even if you describe the subject as a part of a larger class, if your focus is on the part and not the larger class, the part is the subject.

#### Analysis of propositions
##### Categorisation based on quantity & quality
| Quantity/Quality | Affirmative | Negative |
| --- | --- | --- |
| Universal | A-proposition <br><br> All S is P | E-proposition<br><br>No S is P |
| Particular | I-proposition <br><br> Some S is P | O-proposition<br><br>Some S is not P |

Note that the E-proposition is not stated in the form “all S is not P,” because this is an amphibolous statement that can be interpreted in one of two distinct ways: "no S is P" or "some S is not P".

##### Distribution
A term is distributive if the proposition in which it occurs gives some information about every member of the class designated by that term. A distributive statement tells you something about each and every, i.e. all members of a class. An undistributive statement tells you something about some but not all members of a class (some, not only some).

##### Analysing propositions by distributivity of terms
###### A-proposition
**All S is P**

**_Subject S is distributive._**

However, all S may overlap only with a part and not the whole of P. Hence, some P is S but all P may not be S.

**_Predicate P is undistributive._**

###### E-proposition
**No S is P**

**_Subject S is distributive._**

Also, since no S is P, S does not overlap with P. Hence, P also does not overlap with S. Hence, no P is S.

**_Predicate P is distributive._**

###### I-proposition
**Some S is P**

**_Subject S is undistributive._**

Moreover, some S may overlap only with a part and not the whole of P. Hence, some P is S but all P may not be S.

**_Predicate P is undistributive._**

###### O-proposition
**Some S is not P**

**_Subject S is undistributive._**

Now, note that if some S is not P, then at least one of S, say x, is not P. Hence, we know that x is not P, which means no P is x. Hence, we know that each member of P is at least not some member or members of S.

**_Predicate P is distributive._**

###### Summary
For ease of indication:

- d for distributive term
- u for undistributive term

| Proposition type | Subject | Predicate | Symbolically |
| --- | --- | --- | --- |
| A   | d   | u   | All S(d) is P(u) |
| E   | d   | d   | No S(d) is P(d) |
| I   | u   | u   | Some S(u) is P(u) |
| O   | u   | d   | Some S(u) is not P(d) |

#### Relationships
##### For “only S is P”
Only S is P implies:

- Any x that is P can only be S.
- Hence, all P is S.
- Thus, any x that is not S cannot be P.
- Hence, all non-S is non-P.

To summarise,

**Only S is P ≡ All P is S ≡ All non-S is non-P**

##### For different proposition types with identical subjects & predicates
Let S be the common subject and P be the common predicate. Then, we have the following basic propositions:

| Proposition type | Proposition |
| --- | --- |
| A-proposition | All S is P |
| E-proposition | No S is P |
| I-proposition | Some S is P |
| O-proposition | Some S is not P |

For these propositions, we have the following relationships...

###### Contradictory
2 propositions are contradictory if:

- truth of either ⇒ falsehood of the other <br> **AND**
- falsehood of either ⇒ truth of the other

Both cannot be true **AND** both cannot be false.

###### Contrary
2 propositions are contrary if:

- truth of either ⇒ falsehood of the other <br> **BUT**
- falsehood of either cannot determine the status of the other

Both cannot be true **BUT** both can be false, i.e. at least one of them is false.

###### Subcontrary
2 propositions are subcontraries if:

- falsehood of either ⇒ truth of the other <br> **BUT**
- truth of either cannot determine the status of the other

Both can be true BUT both cannot be false, i.e. at least one of them is true.

###### Superimplication & subimplication
Proposition X is the superimplicant of proposition Y if:

- truth of X ⇒ truth of Y <br> **BUT**
- falsehood of X cannot determine the status of Y

X is sufficient but not necessary for Y.

**NOTE**: _Here, Y is the subimplicant of X._

Proposition Y is the subimplicant of proposition X if:

- falsehood of Y ⇒ falsehood of X <br> **BUT**
- truth of Y cannot determine the status of X

###### Summary

Key:

- A, E, I and O are the proposition types
- CD: Contradictory
- CN: Contrary
- SC: Subcontrary
- SP: Superimplicant
- SB: Subimplicant

|     |     |     |     |     |
| --- | --- | --- | --- | --- |
|     | A   | E   | I   | O   |
| A   |     | CN  | SP  | CD  |
| E   | CN  |     | CD  | SP  |
| I   | SB  | CD  |     | SC  |
| O   | CD  | SB  | SC  |     |

#### Translating from informal to formal
Note that:

- Every statement has a subject and predicate
- Any subject or predicate can be defined as a class
- Every informal quantifier is logically either a universal or a particular, i.e. either “all” or “some”
- Every relationship between the subject and the predicate is either affirmative (positive identification) or negative (negative identification)

By these facts, we see that a formal statement can always capture the logical essence of any statement without exception, without losing any logically relevant information.

## Types of arguments and their validations
### Mixed hypothetical argument
Premises are hypothetical and categorical statements, and conclusion is a categorical statement. Its general structure is:

- Hypothetical, i.e. if P then Q
- Categorical affirming/denying the antecedent/consequent
- Conclusion

Note that affirmation or denial is in logical terms, not grammatical terms. The second statement may or may not be in the form of a grammatical negative, but this does not tell us whether or not it is a logical negative. For example:

- If a man is illogical, then he is not happy
- He is happy – denies the consequent “he is not happy”
- ∴ He is illogical

Hence, note that in the hypothetical, the antecedent and consequent terms may include negatives. However, we can validly denote any negative term ¬X as a non-negative term Y = ¬X. Hence, in the presentation of the forms, the symbols P and Q (denoting the antecedent and consequent respectively) shall abstract this detail; P may be some ¬X, and Q may be some ¬Y, but this is not relevant to us.

#### Forms of such an argument
##### Affirming the antecedent
Premises:

- if P then Q
- P

Conclusion:

- ∴ Q is definitely true (P is a sufficient condition for Q)
- ∴ ¬Q is definitely false

_VALID form due to determinate conclusion._

##### Denying the antecedent
Premises:

- if P then Q
- ¬P

Conclusion:

- ∴ ¬Q is indeterminate (P is not a necessary condition for Q)
- ∴ Q is indeterminate (We have no reason to conclude this)

_INVALID form due to indeterminate conclusion._

##### Affirming the consequent
Premises:

- if P then Q
- Q

Conclusion:

- ∴ P is indeterminate (P is not a necessary condition for Q)
- ∴ ¬P is indeterminate (We have no reason to conclude this)

_INVALID form due to indeterminate conclusion._

##### Denying the consequent

Premises:

- if P then Q
- ¬Q

Conclusion:

- ∴ ¬P is definitely true (¬Q is a sufficient condition for ¬P)
- ∴ P is definitely false

_VALID form due to determinate conclusion._

##### Summary
Hence, we have that:

- P → Q ≡ ¬Q → ¬P
- X → Y means X is a sufficient condition for Y

Due to the above facts, we know that only affirming the antecedent or denying the consequent give determinate conclusions, and are hence the only valid forms of mixed hypothetical arguments.

### Pure hypothetical argument
Premises and conclusions are all hypothetical statements. This kind of argument is essentially a chain of implications, with one sufficient condition leading to a result that is itself a sufficient condition for another result and so on. Hence, the conclusion is an implication between the first antecedent and last consequent of the chain. The argument’s general structure is:

Premises:

- X1 → X2
- X2 → X3 <br> ...
- Xn-1 → Xn

Conclusion:

- X1 → Xn

#### Rules for a valid chain
A valid chain is in essence an unbroken chain. Hence, we have the rules:

- Antecedent of conclusion is antecedent of 1st premise.
- Consequent of conclusion is consequent of last premise.
- Consequent of kth premise is antecedent of (k+1)the premise.

The fallacy of broken chain is when a logical chain of hypotheticals is broken. It is crucial to note the order of the terms in each premise, since the order in a hypothetical obviously determines the direction of implication.

#### Translating from informal to formal
- Premises may not be presented in a straightforward manner.
- Watch out for “only if” statements.
- Watch out for statements that, when converted to some equivalent form, would reveal that the chain is in fact valid.

### Alternative argument
Contains one or more alternative premises. Note that there cannot be a pure alternative argument, because alternatives do not by themselves offer one determinate conclusion, neither hypothetically nor categorically.

#### Weak alternative
Given a weak alternative A ∨ B, we have that either one is true, but both can also be true. Hence, we only have a determinate conclusion if one of the alternatives is denied. Affirming either alternative gives an indeterminate conclusion, because both alternatives can be true at once.

#### Strong alternative
Given a strong alternative A ⊻ B, we have that either one is true, but both cannot be true. Hence, we have a determinate conclusion by either affirming or denying any one alternative.

### Categorical argument
Only contains categorical statements as its premises and conclusions. The two basic forms of categorical arguments:

1.  Immediate inference
2.  Syllogism

#### Immediate inference
An immediate inference is an argument wherein the conclusion is drawn from a single premise. By the nature of deduction, such a conclusion is either equivalent to the premise, or a more limited statement than the premise wherein information is lost. The elementary types of immediate inferences are given below.

##### Obversion
The process of changing a proposition into a logically equivalent one having a different quality–i.e. copula is inverted (affirmative to negative, or negative to affirmative).

|     |     |     |     |
| --- | --- | --- | --- |
| Original |     | Obversion |     |
| Type | Form | Type | Form |
| A   | All S is P | E   | No S is P |
| E   | No S is P | A   | All S is non-P |
| I   | Some S is P | O   | Some S is not non-P |
| O   | Some S is not P | I   | Some S is non-P |

##### Conversion
The process of changing a proposition into a logically equivalent one by changing the order of the subject and predicate. In other terms, for a categorical statement "q S c P", we obtain an equivalent statement where S becomes the predicate and P becomes the subject.

|     |     |     |     |
| --- | --- | --- | --- |
| Original |     | Conversion |     |
| Type | Form | Type | Form |
| A   | All S is P | E   | \* Some P is S |
| E   | No S is P | A   | No P is S |
| I   | Some S is P | O   | Some P is S |
| O   | Some S is not P | I   | NA  |

\* _Converse of an A-proposition is not a true converse, because the conversion results in a more limited statement with less information. This is instead called a “converse by limitation.”_

Note that an O-proposition does not have a converse, because, “Some S is not P,” can either mean, “No P is S,” or “Some P is S,”, which are contradictory inferences, neither being even a converse by limitation.

Note also that while, “Some non-P is S,” is equivalent to, “Some S is not P,”, it is not a converse but rather the converse of the obverse–the predicate P is replaced by the new predicate non-P.

##### Contraposition
The process of changing a proposition into a logically equivalent one by obverting the converse of the obverse of the original (i.e. first obverting, then converting the last result, then obverting the last result).

|     |     |     |     |
| --- | --- | --- | --- |
| Original |     | Contraposition |     |
| Type | Form | Type | Form |
| A   | All S is P | E   | All non-P is non-S |
| E   | No S is P | A   | NA  |
| I   | Some S is P | O   | NA  |
| O   | Some S is not P | I   | Some non-P is not non-S |

Here, only the contraposition of an A-proposition is useful to note.

#### Syllogism
A deductive argument containing 2 premises and 3 terms, 2 of which are linked in the conclusion as a result of the linking of each of them with the 3rd (also called middle) term in the premises. In other terms, a syllogism is a categorical argument which connects the terms S, M and P in the manner:

- Premise 1: M–P (or P–M)
- Premise 2: S–M (or M–S)
- Conclusion: S–P

Note that the left-side terms are the subjects, while the right side terms are the predicates. A simple example of such an argument is:

- All men are mortal.
- Socrates is a man.
- ∴ Socrates is mortal

##### Terminology

**Minor term is**:

The subject of conclusion. It is called so because usually–by the nature of most such deductive arguments–the conclusion’s subject is the term denoting the least broad, i.e. narrowest class in the argument.

**Minor premise**:

The premise containing the minor term; it is the 2nd premise.

**Major term**:

The predicate of conclusion. It is called so because usually–by the nature of most such deductive arguments–the conclusion’s predicate is the term denoting the least narrow, i.e. broadest class in the argument.

**Major premise**:

The premise containing the minor term–the 1st premise.

**Middle term**:

The 3rd term of the argument, which links the minor and major terms in the premises.

---

Hence, note the order of the argument’s statements:

- Premise 1: Major premise
- Premise 2: Minor premise
- Conclusion

---

Convention dictates the following notation for the terms:

- Minor term: S (subject of the conclusion)
- Major term: P (predicate of the conclusion)
- Middle term: M

##### 5 rules of syllogistic validity
(Credits to: Aristotle)

###### Rule 1: Three terms rule
The argument must have 3 and only 3 terms.

**Justification**:

A 2-premise argument where each premise relates only 2 terms can make a logical inference from at most 3 terms. **_Any more_**, and we have a case where the premises relate 2 distinct pairs of terms, and thus, are disconnected. Hence, the only logical conclusion possible is an immediate inference from one of the premises, making the other redundant. **_Any less_**, and we have 2 premises with the same subjects and predicates. Hence, either we say everything we could about the terms from the 2 premises alone or we say the same thing about the 2 terms twice–in either case, the only logical conclusion possible is an immediate inference from one of the premises, making the other redundant.

---

**Fallacy of 4 terms**:

Having more than 3 terms in a syllogism.

**NOTE 1** Fallacy of 4 terms can also occur due to equivocation, where one term is used in 2 different senses, resulting in 2 logically different terms.

**NOTE 2**: An apparent fallacy of 4 terms may be resolved if the extra terms are merely logical negations or synonyms of some other term of the argument.

###### Rule 2: Distributive middle rule

The middle term must be distributive at least once in the argument–i.e. at least one premise must inform something about every member of the class denoted by the middle term.

**Justification**:

Only if the middle term (M) is distributive can you either

- link all of M to some/all of the minor term, S, or
- link all of M to some/all of the major term, P

Of course, if either of the above are true, then M is distributive by definition; hence any one of these points is the necessary and sufficient condition for M to be distributive. Now, since M is the only term that can relate S to P across premises, the only way to relate S and P in a syllogism is to ensure that S and P do not link to distinct, non-overlapping parts of M. The only way to ensure this within the argument is to either (1) link all of M to some/all of the minor term, S, or (2) link all of M to some/all of the major term, P. In other words, the only way to ensure this within the argument is if M is distributive. Note again that M needs to be distributive in at least one premise–maybe both, but not none.

---

**Fallacy of undistributed middle**:

Fallacy wherein the middle term is not distributed even once. For example:

- All men are mortal.
- All dogs are mortal.
- ∴ All men are dogs.

###### Rule 3: Matching distributivity rule
If a term is distributive in the conclusion, then it must be distributive in the premises.

**Justification**:

- Distributive ⇒ Some information about all members
- Undistributive ⇒ Some information about some–not all– members

In deduction, you never go from information about some members of a class to information about all members of the class; the essence of deduction is making explicit what is implicit–i.e. contained–in the given premises.

---

**Fallacies**:

**1. Fallacy of illicit minor**:

Minor term is distributed in conclusion but not in premises.

**2. Fallacy of illicit major**:

Major term is distributed in conclusion but not in premises.

###### Rule 4: Two negative premises rule
No conclusion follows from 2 negative premises–negative means either E or O-propositions.

**Justification**:

- Negative of major premise ⇒ Some/all of the major term is outside M
- Negative of minor premise ⇒ Some/all of the minor term is outside M

Hence,

- Some/all of P is not M ⇒  Some/all of P is non-M ... (1)
- Some/all of S is not M ⇒ Some/all of S is non-M ... (2)

But notice that in both (1) and (2), the linking term–which is non-M–is not distributive. Therefore–violating the distributed middle rule–non-M is not a logical, i.e. guaranteed link between S and P, hence no conclusion can be drawn.

_To see it in another way_...

The middle term is disconnected from both terms; all you know about S and P are that they or some part of them is outside M, but there is no logical link between S and P through M.

---

**Fallacy of 2 negatives**:

Fallacy when both premises are negative statements.

###### Rule 5: Negative statement rule
If one premise is negative, then the conclusion must be negative, **AND** if the conclusion is negative, then one premise must be negative.

**Justification**:

If one premise is negative, then either of the following hold:

- Some/all P is outside M and some/all S is inside M
- Vice versa of the above, i.e. some/all S is outside M and some/all P is inside M

Since M is the only link between S and P, and since one of S and P is negatively related to M, the only relationship we can find between S and P through M is negative.

_To see it in another way_...

Since we only know that the part of M related to both S and P is non-overlapping with either some/all of S or some/all of P. Hence, through a link to this part of M, we can only know that some/all of S is non-overlapping with some/all of P. Similarly, if the conclusion shows a negative relationship between S and P, then, since M is the only link between S and P, one of the premises needs to show a negative relationship between either S and M or P and M.

_To see it in another way_...

Since we can only relate S and P through a part of M, if we know that some/all of S is non-overlapping with some/all of P, then it must be that the part of M linking S and P in the premises must be non-overlapping with either S or P.

##### Validating syllogisms in practice
If an argument with 2 premises, 3 terms and a conclusion–i.e. a syllogism– is invalid, then it cannot be anything but invalid because it violates one of the 4 other rules, and–by the nature of the rules of syllogistic validity–such a violation necessarily means that the argument is invalid.

However, if an argument with 2 premises and a conclusion has more than 3 terms, it may be reducible to a valid syllogism only if the extra terms are logical inverses of one of 3 essential terms. To help convert such an argument to a valid syllogism, we can use one or more immediate inferences on one or more of the argument’s statements.

##### General steps for solving a syllogistic argument
1. Translate informal statements–if any–to formal statements.
2. Identify the terms involved–the subject, predicate and middle terms. <br> **NOTE**: _They are generally easiest to identify from the conclusion._
3. Identify & arrange the premises & conclusions in syllogistic order.
4. Restate the statements as a relation between two classes–the resulting copula must be some form of “to be”
5. If there are more than 3 terms, try to reduce them–if possible–by identifying the logical inverses and using immediate inferences.
6. If the argument is a syllogism–i.e. has only 3 terms–then check its validity by referring to the rules of syllogistic validity

### Translating from informal to formal–general points
Purpose of translation is to let the logical structure stand out clearly, and to make the logical relationships clearer. Doing so enables you to grasp (1) the argument’s essence, (2) the argument’s implications, and (3) the basis of the argument’s validity or invalidity. The translation can and should retain all the logical meaning. Hence, identify the implicit terms or presuppositions

# Definition
## Introduction
A definition is a statement that identifies the nature of the units subsumed under a concept. In other terms, definitions serve to give each concept a clear, specific identity in one’s cognitive context, and thus, identify its essence, its referents in reality, and its distinctness from other concepts. For this discussion, the valid method of concept formation and the meaning of concepts is taken for granted.

The fundamental tool of reason, and thus, of logic, is concepts. Concepts are the link between perceptual information and knowledge, i.e. the grasp of (1) the key facts that unite and divide relevant parts of reality–relevant with respect to us and our purposes, of course, and (2) he core causes, attributes and relationships–of and between entities–underlying perceived facts–i.e. the essentials and the fundamentals of perceived reality, i.e. the nature (i.e. identity) of entities, and the derived nature of their actions and interactions. Note that the nature of entities is the cause, their actions the effect.

Concepts are the basic, indispensable means of expanding the range and depth of our cognition, and the primary means to efficiently and effectively expand the use of our finite capacity. A definition is–in essence–an effective condensation of a concept, i.e. a statement that captures the whole relevant context of knowledge conveyed by a concept while minimising the mental units required to hold this context in focus. Hence, definitions are indispensable in the proper grasp, retention and application of concepts. Due to its vital role in cognition in general and logic in particular, we shall explore the rules that enable us to form and validate effective definitions.

## Elaboration of key terms
(Credits to: Ayn Rand)

### Unit
A unit is a member of a group of similar beings. It is the result of viewing an being in relation to similar beings, retaining the similarities and omitting the differences between them. Note that by omitting differences, we do not deny that they exist; rather, we are saying that differences exist but may exist in any quantity–it is only that these particular differences are not considered.

### Concept
The mental integration of units of a certain kind. In other words, a single mental unit–a word or statement–is used to refer to any unit of that kind. Units are identification of a specific–though unlimited–set of concretes, and thus, concepts are the integration of a range of concretes into a single mental unit.

#### Measurement omission
The key aspect of concept-formation is measurement omission, i.e. the process of retaining the shared, distinctive characteristics that unite the members of a group while omitting–not denying but keeping out of focus–the differences in measurements of these characteristics between members of the same group.

#### Concept vs. conceptualisation
Since identity is not dependent on consciousness, the referents of a concept do not change; the concept can refer to any being possessing a certain set of characteristics. In other words, while our knowledge of the referents of a concept–and thus the concept’s definition–is contextual and can change with one’s cognitive context, the referents themselves are what they are–or were, or can be–independent of consciousness. Hence, the concept remains the same for any context.

_In other words_...

Since a concept is the mental integration of a fixed–albeit unlimited–set of referents, a concept as such is not contextual; only our grasp of it is. A concept as such implies any and every piece of knowledge we may learn about its referents–this is made possible by measurement omission. In this regard, a concept is like an arithmetic sequence integrated as an algebraic formulation. Such a formulation refers to an unlimited range of terms possessing a certain characteristic and implies anything we may learn about these terms–either individually or as a whole.

However, we do not form any knowledge, including concepts, in a vacuum. To form a concept, we first need some cognitive context from which we can begin abstracting perceptions into units and integrating these units into concepts. Hence, every concept is grasped in a specific cognitive context. Therefore, the process of conceptualisation is contextual. However, the end product of this process is not simply a sum of our knowledge but a reference to an unlimited range of concretes by implication (mainly through measurement omission).

### Essential vs. fundamental
#### Essential
That which is necessary for a being to be what it is, i.e. necessary for its nature and existence. In other terms, if a feature P is essential to an being S, then the existence of S presupposes P.

#### Fundamental
That which is necessary and sufficient for an being to be what it is for an being to be what it is, i.e. necessary and sufficient for its nature and existence. In other terms, if a feature P is fundamental to an being S, then P leads to S, and the existence of S presupposes P.

---

The difference between an essential and a fundamental can be summarised as a difference between a necessary condition and a necessary cause. Hence, all fundamentals are essentials but not all essentials are fundamental.

### Essential and fundamental characteristics
#### Key terms
**Essential characteristic**:

The characteristic that makes the units of a concept the kind of beings they are, and differentiates it from other kinds of beings. Note that based on the definition of a unit, i.e. a member of a group of similar beings, what makes a unit distinctive, i.e. what makes it similar to each member of its group and also different from each member of other groups. Hence, an essential characteristic is a distinctive characteristic that is necessary to unite the members of a group of similar beings.

**Fundamental characteristic**:

The characteristic which is responsible for the greatest number of a being’s distinctive characteristics; it may not explain everything, but it does explain more than any other.

**Property**:

A characteristic that is the effect, i.e. the derivative of an essential characteristic.

#### Objectivity of essentials & fundamentals
Essentials or fundamentals can only be identified in a certain cognitive context; we cannot identify more than we know. Furthermore, the required context in which we need to retain the nature of the units subsumed by a concept may necessitate a more limited cognitive context, thereby limiting what we may identify as essentials or fundamentals. In either case, we see that essentials and fundamentals are defined with respect to a certain context.

Metaphysically, beings have no essentials and fundamentals as such. Metaphysically–i.e. independent of consciousness–only concretes exist, and the identity of a concrete consists of everything that it is and not just some set of basic features. Essentials and fundamentals are identified based on our conscious purpose and knowledge–they are only relevant to unit-perception. They do refer to reality and are objective, but they are not independent of consciousness as they are the products of a conscious, selective focus on reality.

### Definition
A definition is a statement that identifies the nature of the units subsumed under a concept by means of specifying their essential characteristics. In other words, a definition is the statement of essence. Given that essentials are contextual, so are definitions. A statement that merely identifies facts or truths about the units of a concept or the concept as a whole is not necessarily a definition. To be a definition, the statement has to specify and be limited to the essentials. This restriction arises from the role of definitions in cognition; an effective condensation of a concept that minimises the mental units required to hold the whole context of a concept in focus.

#### Elaborating on the purpose of definitions
A definition has to (1) specify the fundamentals that make the referents of a concept what they are, (2) differentiate the units of a concept from everything else in the relevant cognitive context, and (3) delimit, fix or guide the use and application of a concept in the relevant context.

#### Definition vs. concept
Given their contextual nature, definitions can change with the cognitive context. They are not concepts but means of retaining a concept in a given context. In other words, a concept is not its definition; a definition is the identification of a concept in a given cognitive context, not the concept itself.

#### What a definition is not
##### Ostensive definition
A definition is a conceptual statement and not the mere presentation of instances or examples of a concept. For certain concepts which form the basis of–and are presupposed by–any other concept, we obviously cannot provide conceptual statements to define them as that would result in a circular definition. Examples of such concepts are:

- Sensations and basic perceptions
- Metaphysical axiomatic concepts such as existence, identity, and entity
- Epistemological concepts such as consciousness and free will

In such cases, we have to point to instances and examples to prompt the formation of a concept; this is called an ostensive definition. However, this is not strictly a definition in the way we are using the term.

##### Etymological report
A definition is also not an etymological report. Etymology may provide the definition or may at least provide a guide to form, contextualise or clarify the definition. However, etymology by itself is not a reliable means to reach a definition.

## Rules for valid definitions
Credits:

- Original formulation: Aristotle
- Elaboration regarding concept-formation: Ayn Rand

### Rule 1: Structure rule
A valid definition must contain a genus and a differentia.

**Genus (plural - genera)**:

A class wider than the concept to be defined, which represents the basic identity of the concretes / referents in question.

**Differentia (plural - differentiae)**:

The characteristic(s) which distinguish the concretes / referents in question from all other members of the genus.

---

For example, consider the definition of "human":

Rational (differentia) animal (genus)

#### Validation
##### Basic validation
Concepts are a form of organising perceptual material, which means not just discovering in what ways a class of entities is different from others, but also in what ways this group is similar to others. Hence, whenever a given class is a part of a wider field of entities, the definition must encompass this fact; otherwise, you are defaulting on one of the cognitive functions of concepts.

##### Validation using CCD
2 or more existents can only be differentiated on the basis of mutually exclusive characteristics (since mutually non-exclusive characteristics could exist in both at once, thereby not differentiating them). The only way to ensure such mutual exclusivity is to identify characteristics that are different measurements of the same broader characteristic.

Hence, 2 or more existents can only be differentiated on the basis of a shared commensurable characteristic, wherein each existent contains a different measurement of this characteristic. The set of the essential commensurable characteristics shared by a set of existents is their conceptual common denominator–CCD. Note that the CCD for everything in existence (considered at once) is existence itself. Hence, the process of differentiation is scalable to any level.

By extension, to differentiate a class of beings from everything else requires a broader CCD that unites this class with everything else and thereby differentiates it based on having different measurements of the CCD. Hence, to differentiate a concept from everything else in a given context, we need a broader CCD that unites it with everything else in the context–i.e. we need a genus. However, to actually differentiate this concept, we need the particular distinguishing measurements of the CCD that are present in the concept–i.e. we need a differentia.

##### Combining both validations
Knowledge is contextual, and one’s grasp of a concept necessarily exists in a cognitive context that–ultimately–relates to the sum of our knowledge. To hold the proper context in which a concept is to be grasped, we must understand how it relates to the rest of our knowledge, while also being distinct from the rest of our knowledge. Hence, since a concept integrates a class of beings, we must discover how (1) this class of beings is similar to other classes or kinds of beings, i.e. we must discover the CCD that unites this class to other classes in a sufficiently broad context (sufficiency is based on relevance), and (2) this class of beings is different from other classes or kinds of beings, i.e. we must discover the particular measurements of the CCD of the broader context that differentiate this class from other classes in the given context. (1) implies the need for a genus, while (2) implies the need for a differentia.

#### Hierarchy of genera

Since all knowledge is hierarchical, genera also exist in a hierarchy. For example, consider the classes human, animal and organism.

| Class | Genus of... | Species of... |
| --- | --- | --- |
| Human |  -  | Animal |
| Animal | Human | Organism |
| Organism | Animal |  -  |

#### Specificity of genera
The genus of a class must be identified with respect to the relevant cognitive context–this may be the general available context, or simply the specific required context. For the highest cognitive efficacy, you must select as a genus the wider class that shares the greatest number of characteristics with the definiendum (i.e. the class being defined) in the relevant cognitive context.

---

**Specific genus**:

The wider class that shares the greatest number of non-distinctive characteristics with the definiendum.

**Fundamental differentia**:

The distinctive characteristic(s) that leads to or is responsible for the greatest number of distinctive characteristics–distinctive within the specific genus.

---

In simple terms, the purpose of definitions dictates that we must (1) choose the most specific genus possible–in the relevant context, and (2) choose the broadest, i.e. most fundamental differentia possible–in the relevant context.

**NOTE**: _The fundamentality of the differentia is elaborated in the rule of fundamentality._

##### How context determines specificity
Always note that the required or relevant context determines the specificity required. For example, regarding the definition of “human,” “rational animal” is sufficient for philosophical discussion; it defines the fundamental characteristics required to discuss human nature in broad philosophical terms. Here, “rational mammal” would entail needless specificity; there is nothing philosophically relevant in the genus “mammal” that is not also present in the genus “animal.” However, for the purposes of biology, we may require more specificity, and thereby define “human” as “rational mammal,” “rational primate,” etc.

In other words, define the genus based on the specific category of beings you want to or need to distinguish your concept from, i.e. find the minimum conceptual common denominator (CCD) required. For example, when defining “socialism,” we need to define it within the broader category of “political systems” rather than overly broad categories such as “ideas” or “systems.”

### Rule 2: Equivalence rule
The definiendum (the class being defined) and the definiens (the definition offered) must be logically equivalent in the given context. In other words, for the given context, the definition must be true (a) for all members of the definiendum and it must be true (b) only of the members of the definiendum.

#### Validation
Note that (a) is necessary to integrate the class–to specify what makes each member of the class a part of the same class–and (b) is necessary to differentiate the class–to specify what makes each member of the class distinct from members of other classes.

#### Fallacies due to violation
Violating this rule can result in one of 2 fallacies:

1. Definition is too narrow–condition <br> (a) is violated, i.e. the definition includes too little.
2. Definition is too broad–condition <br> (b) is violated, i.e. the definition includes too much.

To avoid making these fallacies, here are some tips. (1) Let your mind range over a wide variety of examples, both within the class being defined, and outside the class being defined; variety is key. (2) To avoid violating condition (b) in particular, keep in mind the specific broader category of beings you want to differentiate your concept from (by keeping in mind a certain cluster of associated concepts)–i.e. keep the appropriately defined genus in mind (not too narrow or too broad).

#### Checking equivalence
Your definition is logically equivalent when you can assert it as an A-proposition which is simply convertible. Note that an A-proposition is a categorical statement of the form "all S is P". This proposition is not simply convertible by itself, since from "all S is P", you can only determine that "some P is S "and not that "all P is S". A simply convertible A-proposition is such that you can say from "all S is P" that "all P is S". In other words, you should be able to assert your definition in both ways:

- All S is P ( condition (a) is fulfilled)
- All P is S (condition (b) is fulfilled)

### Rule 3: Fundamentality rule

The definition must state fundamental characteristics.

i.e.

The essential characteristics used in the definition must also be the fundamental characteristics (in the given context).

#### Validation

This rule becomes crucial if the units of a given concept have a number of distinctive characteristics, each true for all units and only true for those units in the given context. The purpose of a definition is, in essence, unit-economy in cognition. This means that a definition must be a proper condensation of a concept and not a catalogue of its distinctive characteristics.

In short, the fundamental characteristics, due to their relatively wide-ranging implications about the nature of the units of a concept, makes them the only rational choice of differentia. In other words, fundamental characteristics have the most causal significance, and thus, imply the widest context possible with the fewest possible mental units. Consequently, grasping the fundamental characteristics enables you to understand the properties and behaviour of a being in the widest range of situations and circumstances.

#### Identifying the fundamentals

- If we have one or more distinctive characteristics, find out what distinctive character–either among them or some other–is the root or cause of them that is itself a distinctive characteristic.
- If a distinctive characteristic does not have any roots that are also distinctive to the class, this does not mean this characteristic is fundamental.

#### Fundamentality being the fundamental rule

The rule of fundamentality is implicit in every other rule, i.e. the violation of any other rule automatically violates this one. Hence, to pinpoint the flaws in a definition, first check with every other rule.

### Rule 4: Circularity rule

The definition must not–directly or indirectly–define the subject by the subject itself.

i.e.

The definition must not contain any content that, to understand, presupposes the definition you are supposed to be providing.

#### Ways to commit circularity

- Naming the concept being defined or its derivatives.  
    Ex. Democracy: “A system of governance in which leaders are elected democratically.”
- Use of synonyms or synonymous expressions for the concept being defined or its derivatives.  
    Ex. Lateness: “The state of tardiness.”

- Note that synonyms can help if you already have a definition for the concept and just need to connect this definition with the synonyms.

- Use of concepts whose own definitions require the concept being defined.
- Use of correlatives–i.e. 2 things so related that the existence of either implies the existence of the other  
    Ex.

- Cause: “That which creates an effect.”  
    Effect: “That which is caused by something.”
- Husband: “A man who has a wife.”  
    Wife: “A woman who has a husband.”

##### Defining correlatives

In defining correlatives, do not define one correlative in terms of the other–the 2-way implication between them means no new information is conveyed. Instead, define the actual essence of the correlatives, i.e. define the relationship between the correlatives. So, for example, to define “husband” or “wife,” first define the underlying relationship, i.e. “marriage.” Thus, define the correlatives with respect to their relationship.

### Rule 5: Negatives rule

Where possible, a definition must be positive not negative.

i.e.

As far as possible, a definition must identify what something is rather than what it is not.

#### Validation

Identity is defined by the existence of specific attributes. Thus, identification of concepts, i.e. definitions, must involve identification of what exists. Note that even when identifying a concept in negatives, you are necessarily identifying it with respect to positives; an absence can only be identified if you know what could be present but is not. Furthermore, confirming a positive is far easier than confirming a negative, since a positive is immediately identifiable while a negative can only be identified by first confirming what is in fact present.

Hence, positive definitions must precede negative ones, and choosing a negative definition where a positive one is available reduces your ability–in terms of efficiency and effectiveness–to identify and apply the concept.

---

Note that there are certain concepts that can only be defined in terms of negatives, due to the only differentia possible in the given context being the absence of certain characteristics. For example,

- Bachelor: “Unmarried (i.e. not married) man.”
- Vacuum: “Space without matter.”
- Orphan: “Person without parents.”
- Bald: “Absence of hair.”

### Rule 6: Obscurity rule

(This is a general rule for thought, not just definitions)

Definition must be stated literally (i.e. with exact meaning), clearly and simply and economically as possible–for the relevant context

i.e.

Say what you mean and mean what you say, directly, clearly, exactly and simply as possible–for the relevant context.

#### Validation

An unnecessarily complicated, difficult-to-retain definition defeats the essential purpose of a definition: achieving unit-economy by effectively condensing the nature of a concept in a given context.

Note that legitimate technicality or specificity in the appropriate context is not necessarily a violation of this rule, even if the definitions seem overly complicated for someone outside this context. For example, the definitions of organs in medical terms would be far more specific and detailed than a layman’s definitions due to the different requirements of their respective contexts.

Hence, the rule of obscurity, like every other rule, applies within a given context.

### Final notes on validating definitions

- Invalidating a definition does not mean you will be presented with a valid one. Furthermore, to invalidate a definition, you do not need to know the valid one.
- You can neither make nor validate definitions out-of-context. Hence, every rule for the validity of definitions is to be applied within a given cognitive context.

## Tips for forming valid definitions

- If unsure about how to define a concept in your specific context, start with a simpler, broader context and then narrow down.
- Note the purpose of a definition in studies:  
    To integrate and isolate an unlimited range of concretes for the purposes of specialised study.With this in mind, ask:

- Is the definition too generalised so as to be useless for specialised study?  
    i.e.  
    Does the definition name a fundamental similarity that warrants a specialised study, or does it integrate too disparate a set of things so that it is not meaningful to study the concept in a more intensive context?  
      
    Note: The broader the scope of a definition, the less useful it is in a more intensive context.

---

# Inductive reasoning

The essence of induction is the inference of generalisations (universals) from perceived instances (particulars). Deduction requires universals, and thus, presupposes induction; induction, thus, is the primary process of reasoning.

Hence, human cognition without generalisation is a self-contradiction as it would have to be human cognition without induction, and thus, without deduction — hence, it would have to be cognition without reason, i.e. non-cognition.

## Definitions & clarifications

### Generalisation

A proposition that ascribes an attribute to every member of an unlimited class (if the class were limited, it would be enumerable, making inference unnecessary since you can learn about each member individually).

In formal terms, a generalisation is a proposition of the form:

All S is P

NOTE: No metaphysical proposition is a generalisation, since a metaphysical proposition only reaffirms the fact of existence and identity as such; no metaphysical proposition abstracts and ascribes attributes of particulars to every member of an unlimited class. Corollaries of metaphysical propositions are clearly also not generalisations, as they are merely recontextualisations of the metaphysical propositions.

### Concept vs. generalisation

Both concepts and generalisations are means of extending cognition beyond perception. Concepts are the means of integrating a range of concretes; integration here means to make a range of concretes retainable and accessible by a single mental unit. Concepts by themselves serve to organise existing knowledge (ex. to organise existing perceptions into a potentially unlimited category), not to claim new knowledge. Generalisations, on the other hand, are the means to gain new knowledge about the referents of concepts.

Thus, while a concept is either valid or invalid but not true or false, a generalisation is either true or false rather than valid or invalid.

## The problem of induction

Can observations of particulars ever lead to knowledge — with certainty — about the broader category under which the particulars lie? If so, how?

## Axioms of induction

A method of reasoning must begin with axioms, i.e. propositions irreducible by logic (as they are the basis of logic). Such axioms must be (1) self-evident and (2) sufficient to cover every area the method of reasoning can explore.

For any method of reasoning, we begin — before any generalisation — at the laws of logic and perceptions (which are self-evident). For deduction, we are also given generalisations as premises and — often — observations of particulars. However, we are not given generalisations at the start of induction, since induction is the method of reaching generalisations. To move from perceptions to generalisations, we need a means to integrate an unlimited class of perceptions, i.e. we need concepts. First-level concepts are concepts whose referents are perceptions, and thus, this is where we begin inductive reasoning.

However, concepts by themselves are not new knowledge but the integration of knowledge already acquired and that can be acquired. Hence, to say something new, we need to apply concepts to observations that reveal the attributes and actions of one or more entities not previously integrated into the concept (since attributes and actions are the aspects of an entity or a class of entities that describe parts or the whole of its identity in some context). In other words, we must observe the expression of an entity’s identity (through attributes or actions) that reveal something more about the identity of a class of entities than was previously known in the concept alone.

---

EXAMPLE: The concept of swans may be integrated by essentials that do not include their colour (since their colour is not key causal factors in their nature and are not necessitated by other similarities). If we find that, for some reason inherent in the nature of the swans, they have to be white, then the generalisation would be “all swans are white” (which is not true of course, but this is just an example), which is knowledge that goes beyond the concept of swan itself.

---

The law that relates facts of reality to each other is the law of causality, which says that things can only act by their nature. Hence, it is through the discovery of causality that we discover something beyond known facts about a kind of thing so as to discover something that is necessitated by the nature of that kind of thing but was not evident in perception alone. Thus, generalisation must begin with applying concepts to perceived causal relationships.

---

Can causal relationships be perceived? If so, to what extent?

A perceptual causal relationship is the perception of an action of an entity or an interaction between two or more entities necessitated by the nature of the entities involved. If I push an object and the object moves, these actions are not only sequential but also causal, since it is self-evident that solid matter (which my hand and the ball both consist of) resists — and thus can impact — other solid matter. A first-level generalisation is a generalisation of a perceptual causal relationship, and it is formed by applying concepts to the causal relationship, i.e. retaining its essence and omitting its measurements.

NOTE: Applying concepts only lead to a generalisation if the essence of the causal relationship is retained by the concepts being applied. For example, I can validly say, “fire burns paper” upon observing it, but it is invalid and thus false to say, “orange, waving things burn white, flat things”; the former retains the essence of the causal relationship (the heat and the material) whereas the latter omits it.

Hence, the axioms of induction are first-level generalisations, which — through a similar method of abstraction — can lead to higher-level generalisations.