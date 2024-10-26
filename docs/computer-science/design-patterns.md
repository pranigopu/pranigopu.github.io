[<< Back to **Computer Science**](https://pranigopu.github.io/computer-science)

**DESIGN PATTERNS**

---

**Contents**:

- [Introduction](#introduction)
- [Categories of design patterns](#categories-of-design-patterns)
- [Creational design patterns](#creational-design-patterns)
  - [Singleton](#singleton)
  - [Factory](#factory)
  - [Prototype](#prototype)

---

> **Key indirect reference**: _Design Patterns: Elements of Reusable Object-Oriented Software_ by Gamma Erich, Helm Richard, Johnson Ralph and Vlissides John

---

# Introduction
Design patterns (or more precisely, software design patterns) are reusable solutions to recurring challenges in software design. These solutions are reusable because they are generalised, i.e. they are abstract solutions that serve as templates or conceptual frameworks rather than exact solutions (hence the use of term "pattern"). Design patterns offer the following values:

<details><summary>Reusability</summary>Design patterns are reusable solutions to common problems, reducing the need to rediscover solutions/solution components and promoting code reuse.</details>

<details><summary>Flexibility</summary>Being generalised, design patterns are adaptable and can be tailored to specific requirements.</details>

<details><summary>Communication</summary>Design patterns establish a shared language among developers, improving communication and collaboration.</details>

<details><summary>Maintainability</summary>Design patterns conceptually integrate and help organise various parts of a solution, which can lead to code that is easier to understand, modify and extend over time, thus making it more maintainable.</details>

> **Reference**: [_Software Design Patterns 101: A Beginner's Guide_ by Digicore from **Medium.com**](https://medium.com/@digicore/software-design-patterns-101-a-beginners-guide-c6860ef8bb63)

# Categories of design patterns
<details><summary>Creational</summary>Patterns for object creation (i.e. class instantiation).</details>

<details><summary>Structural</summary>Patterns for object implementation (i.e. use of class features and functions).</details>

<details><summary>Behavioural</summary>Patterns for object interaction (e.g. communication, management, etc.).</details>

> **References**:
>
> - [_Gangs of Four (GoF) Design Patterns_ by Pankaj from **DigitalOcean.com**](https://www.digitalocean.com/community/tutorials/gangs-of-four-gof-design-patterns) <br> **NOTE**: _"Gangs of Four" should be "Gang of Four"._

# Creational design patterns
## Singleton
The singleton pattern is a pattern wherein a class has only one instance, provides an easy point of access to it and restricts the creation of more than one instance. Examples of use cases: logging (where there exists a single instance of a logger, so that all logs are held in one space), device drivers (i.e. a computer program, which controls or operates a particular type of device that is attached to the computer; reference: [_Device Drivers_ **TeachComputerScience.com**](https://teachcomputerscience.com/device-drivers)), caching (where there exists a single instance of a cache manager, so that there are no conflicts in memory reads and writes), etc.

A singleton design pattern has two broad implementation approaches: (1) eager initalisation and (2) lazy initialisation. In eager initialisation, a class is instantiated as soon as it is loaded by the system during runtime; this can be useful if the object is expected to be used frequently. In lazy initialisation, a class is instantiated only when required during runtime; this can be useful in saving memory and improving the overall performance of the program.

> **References**:
>
> - [_Java Singleton Design Pattern Best Practices with Examples_ by Pankaj and Bradley Kouchi from **DigitalOcean.com**](https://www.digitalocean.com/community/tutorials/java-singleton-design-pattern-best-practices-examples)
> - [_Singleton design pattern in Java_ from **javapoint.com**](https://www.javatpoint.com/singleton-design-pattern-in-java)

## Factory
A design pattern that abstracts the instantiation of objects through a factory method, i.e. it is a design pattern that enables the creation of the required objects without specifying the required classes. The factory method identifies and instantiates the required class based on a set of conditions or parameters passed to it by the client code (i.e. the program or program component that requests the class instantiation).

> **References**:
>
> - [_From Concept to Creation: Understanding Factory Design_ by Avinash Tingre from **Medium.com**](https://medium.com/javarevisited/design-patterns-101-an-introduction-to-factory-1929a5d124af)
> - [_Factory method pattern_ from **Wikipedia**](https://en.wikipedia.org/wiki/Factory_method_pattern)

## Prototype
A design pattern consisting of the cloning of an existing object instead of creating new one such that the cloned object can also be customised. This pattern is useful if the cost of creating a new object is resource intensive. Some advantages of this pattern are given below:

- Reduced need for subclassing
- Avoids the complexity of class instantiation
- Hence, new objects can be obtained without knowing the class
- Objects can be easily added and removed in runtime

Hence, we see that this design pattern lets us copy existing objects without making our code dependent on their classes, since we are not instantiating any class when copying and modifying the objects. Evidently, prototyping is a practical alternative to cases where we may otherwise need to use subclasses, since prototyping copies and modifies existing objects that are similar to the desired object.

> **References**:
>
> - [_Prototype Design Pattern_ from **javapoint.com**](https://www.javatpoint.com/prototype-design-pattern)
> - [_Prototype_ from **refactoring.guru**](https://refactoring.guru/design-patterns/prototype)
> - [_Prototype Design Pattern in Java_ by Pankaj from **DigitalOcean.com**](https://www.digitalocean.com/community/tutorials/prototype-design-pattern-in-java)