--- 
title: Bisection Method
draft: false
tags: [Applied Mathematics, C++, Root Finding, Bisection Method]
---


# Root Finding with the Bisection Method
>**GOAL** Build a program that can perform Bisection Method to find roots of a function.

>**WHY** To have my own root finding program that I can modify and use.

>**REPO** <not set up yet> 
--- 

## High-Level Structure 
- [Introduction](#introduction)
- [Root Finding](#root-finding)
- [Program Design and Workflow](#program-design-and-workflow)
- [Future Implementations](#future-implementations)
- [Conclusion](#conclusion)
- [Contact](#contact)
---

## Introduction
Root-finding is a fundamental task in mathematics (engineering, physics, computer science). We 
often need and use numerical methods when analytical solutions (traditional algebraic methods) take too long or are impossible to find the root. These Numerical Methods can be used when optimizing functions, solving physical problems, computer graphics and modelling complex systems.

## Root Finding
### Bisection Method Algorithm
Mathematically, the Bisection Method is a bracketing root-finding algorithm that requires the equation of the form $f(x)=0$ meets the requirement of the Intermediate Value Theorem, which states:

* Function $f(x)$ is a continuous function on the interval $[a, b]$.
* An interval $[a,b]$ such that $f(a)f(b)<0$
* Halve the interval interactively until the function converges to the root.
$$
\text{Midpoint: } c = \frac{a+b}{2}
$$
* If $f(a)f(c)<0$ then the root lies in the interval $[a,c]$.
  - Otherwise, the root lies in the interval $[c,b]$.
* Repeat the process until the interval width is below the tolerance level or the maximum number of iterations is reached.

The downsides of the Bisection Method is that if a function doesn't meet the IVT then this method cannot guarantee convergence to the root. Furthermore, this method is slower that other root finding methods such as Newton-Raphson or Secant Method.

## Program Design and Workflow
1. **Equation Input:**
  - The user can enter one or more functions in terms of x in a symbolic format (e.g., "x^2 - 4").
  - Results include root approximation, number of iterations, Bisection method calculated.

2. **Root Finding:**
  - Bisection Method is implemented to find the root of the user provided function(s).
  - The user can specify the tolerance level and number of iterations can be automatically calculated using the below formula.

$$
\begin{equation*}
n\geq \frac{\log_{10}(b-a) - \log_{10}(\varepsilon)}{\log_{10}(2)}
\end{equation*}
$$

3. **LaTeX Report Generation:**
  - The program generates a LaTeX report that includes:
    - The original function(s) provided by the user.
    - The steps taken in the Bisection Method.
    - The final root approximation and number of iterations.

This program design ensures reproducibility and readability of the results, making it easier for users to understand the process and outcomes of the Bisection Method.

# Conclusion
I have hopes this project will be a valuable tool for anyone needing to find roots of functions numerically. This project aims to bridge numerical analysis with software engineering and technical reporting. By combining robust numerical methods, automated LaTeX reporting, clean C++ architected implementation and produces results that are mathematically sound and professionally presentable 

### Contacts
email: [mokhaditlhalefo@gmail.com](mailto:mokhaditlhalefo@gmail.com)

LinkedIn: [linkedin.com/in/mokhaditlhalefo](https://www.linkedin.com/in/tlhalefo-mokhadi-445b05226/)
