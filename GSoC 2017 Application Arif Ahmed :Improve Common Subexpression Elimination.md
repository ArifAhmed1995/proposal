# Improving Common Subexpression Elimination in SymPy
------------------------
### About Me :
#### Contact Information :
 - **Name** : Arif Ahmed
 - **University** : [Birla Institute of Technology and Science, Pilani](http://www.bits-pilani.ac.in/)
 - **Short Bio** : Undergraduate Student of **MSc(Hons.)Mathematics** and **B.E(Hons.)Computer Science**
 - **Email-Id** : arif.ahmed.5.10.1995@gmail.com
 - **GitHub username** : [ArifAhmed1995](https://github.com/ArifAhmed1995)
 - **Time-Zone** : UTC + 5:30
#### Personal Information :
My name is Arif Ahmed, a second year undergraduate student at BITS Pilani Goa Campus,India. Currently I am pursuing a degree in Mathematics and Computer Science.

I use Linux Mint 18 on my workstation and PyCharm IDE for any Python related work.I have been coding in Python(more than a year),Java(> 2 years), C/C++(more than a year) and R(about a year).I like Python the best of all the languages mentioned because it's easier to work with as compared to the others.

Along with Mathematics courses such as Complex Analysis, Discrete Mathematics, Optimization, Introduction to Algebra, Real Analysis, etc I have also completed various online courses related to Computer Science and Programming.

###### The online courses successfully completed are :
 - [Algorithms I - Princeton University](https://www.coursera.org/learn/algorithms-part1)
 - [Introduction to Interactive Programming in Python(Part I) - Rice University](https://www.coursera.org/learn/interactive-python-1)
 - [Introduction to Interactive Programming in Python(Part II) - Rice University](https://www.coursera.org/learn/interactive-python-2)
 - [Principles of Computing(Part I) - Rice University](https://www.coursera.org/learn/principles-of-computing-1)
 - [Principles of Computing(Part II) - Rice University](https://www.coursera.org/learn/principles-of-computing-2)
 - [First Five Courses of Data Science Specialization - Johns Hopkins University](https://www.coursera.org/specializations/jhu-data-science)
 - [Machine Learning - Stanford University](https://www.coursera.org/learn/machine-learning)

As part of the above courses I had to code various weekly assignments in Java, Python and R.The most exciting assignments were the ones in Rice University's courses. Unfortunately I cannot upload the assignments on GitHub as the instructors forbade us to do so. In my freshman year, I made a simple simulator in Java which simulates [Diffusion of a Gas](https://github.com/ArifAhmed1995/Mini-Projects/blob/master/Simulation.java).

Ever since I was introduced to Python, I liked it far better than any other language. The reason is that one can think more about converting ideas to working code rather than wrestling around with the nuances of the language.One of the advanced features about Python that I liked was the `lambda` operator/function.It's a nifty way to create a nameless function.

One the best features I like about SymPy is it's capability to solve difficult integration problems, especially Indefinite Integrals :

```
>>> Integral(x**3*sin(x*2), x).doit()
-x**3*cos(2*x)/2 + 3*x**2*sin(2*x)/4 + 3*x*cos(2*x)/4 - 3*sin(2*x)/8

```

I am quite familiar with Git/GitHub and have learnt a lot about it whilst opening Pull Requests, creating branches, squashing commits, etc

### Contributions :
I stumbled upon SymPy while searching for Mathematics related projects on GitHub, sometime around 20th September 2016.I have attempted to fix issues and understand SymPy's codebase since then.Here is a list of my pull requests :

##### Merged:
 - [Geometric sum evaluates correctly for float base](https://github.com/sympy/sympy/pull/11682) **(Easy to Fix)**
 - [ring series : rs_series extended for logarithm](https://github.com/sympy/sympy/pull/12274) **(Easy to Fix)**
 - [matrices : re and im works for matrices](https://github.com/sympy/sympy/pull/11696)
 - [core.evaluate : evaluate(False) works for numeric operators](https://github.com/sympy/sympy/pull/11714)
 - [evalf : Increase depth of symbolic evaluation for Abs()](https://github.com/sympy/sympy/pull/11970)
 - [solvers/solveset: Rectify solution for some special equations](https://github.com/sympy/sympy/pull/12040)
 - [ntheory : Add functionality to return list of all factors](https://github.com/sympy/sympy/pull/12194)
 - [integrals : Add singularity test for Zero denominator](https://github.com/sympy/sympy/pull/12142)
 - [core : Float constructor allows to set binary or decimal precision](https://github.com/sympy/sympy/pull/12227)

##### Unmerged :
 -  [matrices:Matrix**exponent stays unevaluated for non-integer exponent](https://github.com/sympy/sympy/pull/11648) **(Needs Decision)**
 -  [matrices : Add an optional key 'evaluate' for MatPow.](https://github.com/sympy/sympy/pull/11843) **(Needs Decision)**
 -  [[WIP]sympify : Added support for translating basic numpy datatypes](https://github.com/sympy/sympy/pull/11666)
 -  [Matrix : transpose of non-complex symbol returns the same symbol](https://github.com/sympy/sympy/pull/11733)
 -  [solve : Solutions with spurious real/imaginary terms are discarded)](https://github.com/sympy/sympy/pull/11815)
 -  [Add contour plot function](https://github.com/sympy/sympy/pull/12049)
 -  [vector : Add Laplacian and Vector Laplacian support](https://github.com/sympy/sympy/pull/12261)

### Introduction :
----------
In the field of Compiler Theory, common subexpression elimination(CSE) is an optimization technique that searches for identical expressions(i.e. evaluates to the same value) within a larger one and analyzes if total evaluation time is decreased when those identical expressions are replaced by a single variable.

From the perspective of a Computer Algebra System(CAS) , CSE serves as a backend to pre-process the input expression and pass on a new optimized one to the solver layer. This in turn , saves computation time [[1]](https://books.google.co.in/books?id=mB_9g2c29ikC&pg=PA66&dq=EUROCAL+%2787:+European+Conference+on+Computer+Algebra,+Leipzig+Page+66&hl=en&sa=X&ved=0ahUKEwjTyMjYxt_SAhUMpo8KHZhcBOEQ6AEIGzAA#v=onepage&q&f=false)


### Definitions and Keywords :
-----------
 - **Literal** - A variable or constant(e.g : \(2, 92, x, a, y\)).
 - **Cube** - A product of literals each raised to some non-negative integer power along with an associated positive or negative sign(e.g : \(+3 a^{2} b\), \(-2 a^{3} b^{2}\)).
 - **SOP representation** - SOP stands for Sum of Products.In the context of polynomials, it is simply a sum of cubes.
 - **Cube-Free** - A SOP expression is cube-free when there is no cube(except for the cube `1`) that divides all cubes of the expression.
 - **Kernel** - Given a polynomial `P` and a cube `c`, the expression `P/c` is a kernel if it is cube-free and is constituted of at least two terms.(e.g : If \(P = +3 x^{4} y^{2} - 4 x^{5} y^{3} \) and \(c = x^{4} y^{2}\) then \(P/c = 3 - 4 x y\) is a kernel).
 - **Co-Kernel** - The cube utilized to obtain a kernel is a co-kernel. In the above example \(c = x^{4} y^{2}\) is the co-kernel.
### The Project Idea :
----------------
My propsal is to implement an efficient algorithm to perform CSE on an input expression. The data structures and workflow is detailed here : [[2]](http://cseweb.ucsd.edu/~kastner/papers/tech-poly_factorization_cse.pdf).
### Importance of the Project :
-----------
As mentioned earlier, a robust CSE saves computation time by pre-processing expressions thereby making it easier for the solver layer. For a CAS, expressions can be interpreted as polynomials.For example, \((\sqrt{\sin{(a)}} - 3)\)\((\sin{(a)} + 4)\) can be expressed as a polynomial : \((t - 3)\)\((t^{2} + 4)\) with \(t = \sqrt{\sin(a)}\)

Since continuous functions can be approximated by polynomials to a desired degree of accuracy , such polynomials are used as a replacement in many applications.[[2]](http://cseweb.ucsd.edu/~kastner/papers/tech-poly_factorization_cse.pdf).

The current CSE implementation in SymPy is lacking.Consider the following examples : 
```
>>> z, u, a, v, b, w, q = symbols('z u a v b w q')
>>> P = z*u**4 + 4*a*v*u**3 + 6*b*u**2*v**2 + 4*u*v**3*w + q*v**4
>>> cse(P)
([(x0, u**2), (x1, 4*u*v), (x2, v**2)], [a*x0*x1 + 6*b*x0*x2 + q*v**4 + u**4*z + w*x1*x2])
```

As mentioned here [[3]](https://github.com/sympy/sympy/wiki/Code-Generation-Notes), SymPy's current CSE method is used in many open source projects and as such would greatly benefit if the algorithm is improved.

### Design :
---------------------


### Implementation Details :
-------------------------
#### Phase I :
#### Phase II :
#### Phase III :
### Timeline :
-----------
### How do I fit in ?
-------------
### References :
------------------