# Improve Code Generation in SymPy
------------------------

**Note : This is currently incomplete and only includes the procedure for improving the CSE function in SymPy(a major part of the project). I will add more improvements to codegen soon.**

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

##### The online courses successfully completed are :
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

From the perspective of a Computer Algebra System(CAS) , CSE serves as a backend to pre-process the input expression and pass on a new optimized one to the solver layer. This in turn , saves computation time [[3]](https://books.google.co.in/books?id=mB_9g2c29ikC&pg=PA66&dq=EUROCAL+%2787:+European+Conference+on+Computer+Algebra,+Leipzig+Page+66&hl=en&sa=X&ved=0ahUKEwjTyMjYxt_SAhUMpo8KHZhcBOEQ6AEIGzAA#v=onepage&q&f=false)


### Definitions and Keywords :
-----------
 - **Literal** - A variable or constant(e.g : `2, 92, x, a, y`).
 - **Cube** - A product of literals each raised to some non-negative integer power along with an associated positive or negative sign(e.g : `3*a**2*b`, `-2*a**3*b**2`).
 - **SOP representation** - SOP stands for Sum of Products.In the context of polynomials, it is simply a sum of cubes.
 - **Cube-Free** - A SOP expression is cube-free when there is no cube(except for the cube `1`) that divides all cubes of the expression.
 - **Kernel** - Given a polynomial `P` and a cube `c`, the expression `P/c` is a kernel if it is cube-free and is constituted of at least two terms.(e.g : If `P = 3*x**4*y**2 - 4*x**5*y**3` and `c = x**4*y**2` then `P/c = 3 - 4*x*y` is a kernel).
 - **Co-Kernel** - The cube utilized to obtain a kernel is a co-kernel. In the above example `c = x**4*y**2` is the co-kernel.
### The Project Idea :
----------------
My propsal is to implement an efficient algorithm to perform CSE on an input expression. The data structures and workflow is detailed here : [[1]](http://cseweb.ucsd.edu/~kastner/papers/tech-poly_factorization_cse.pdf).
### Importance of the Project :
-----------
As mentioned earlier, a robust CSE saves computation time by pre-processing expressions thereby making it easier for the solver layer. For a CAS, expressions can be interpreted as polynomials.For example, `(sqrt(sin(a)) - 3)(sin(a) + 4)` can be expressed as a polynomial : `(t - 3)(t**2 + 4)` with `t = sqrt(sin(a))`

Since continuous functions can be approximated by polynomials to a desired degree of accuracy , such polynomials are used as a replacement in many applications.Look at the `Introduction` in : [[1]](http://cseweb.ucsd.edu/~kastner/papers/tech-poly_factorization_cse.pdf).

The current CSE implementation in SymPy is lacking.Consider the following examples :
```
>>> z, u, a, v, b, w, q = symbols('z u a v b w q')
>>> P = z*u**4 + 4*a*v*u**3 + 6*b*u**2*v**2 + 4*u*v**3*w + q*v**4
>>> cse(P)
([(x0, u**2), (x1, 4*u*v), (x2, v**2)], [a*x0*x1 + 6*b*x0*x2 + q*v**4 + u**4*z + w*x1*x2])
```
Normally a implementation of CSE should yield : 
```
>>> cse(P)
([(x0, u**2), (x1, u*v), (x2, v**2)], [4*a*x0*x1 + 6*b*x0*x2 + q*x2**2 + x0**2*z + 4*w*x1*x2])
```
The algorithm mentioned in the paper would yield a more optimized result : 
```
>>> cse(P)
([(x0, u**2), (x1, 4*u)], [u*(u*z + a*x1)*x0 + v**2*(u*(x1*w + v*q) + 6*b*x0)])
```
In some cases SymPy's existing CSE doesn't work at all : 
```
>>> x = Symbol('x')
>>> p = x - x**3 + x**5 - x**7
>>> cse(p)
([], [-x**7 + x**5 - x**3 + x])
```
As per the algorithm we get a far more optimized result :

```
>>> x = Symbol('x')
>>> p = x - x**3 + x**5 - x**7
>>> cse(p)
([(x0, x**2)], [x*(1 - x0*(1 - x0*(1-x0)))])
```
```
Original:
Multiplications -> 12
Additions/Subtractions -> 3

Optimized:
Multiplications -> 4
Additions/Subtractions -> 3
```
As mentioned here [[2]](https://github.com/sympy/sympy/wiki/Code-Generation-Notes), SymPy's current CSE method is used in many open source projects and as such would greatly benefit if the algorithm is improved.

### Design :
---------------------
The important data structures to be used in this project are : 

 * **Kernel Cube Matrix(KCM)**

    Used in detection of multiple cube common sub-expressions.
 
    The input expression is acted upon by the Kernelling Algorithm(details mentioned later). A set of kernels and corresponding co-kernels are
    generated.A matrix is constructed whose rows denote the co-kernels generated and columns denote distinct cubes which constitute a particular 
    kernel.
    
    `KCM[i, j] = 1`, if the product of co-kernel in row `i` and the kernel cube in column `j` yields a product term in the original set of 
    expressions.
    
    We define a `rectangle` as a set of row and columns of the KCM such that all the elements are 1. The `value` of the rectangle is the
    weighted sum of the number of operations saved by selecting the common sub-expression or factor corresponding to that rectangle.
    Therefore , selecting the optimal set of common subexpressions and factors is equivalent to finding a maximum valued rectangle of the KCM.
    A prime rectangle is one which is not covered by any other rectangle and thus yields more value than any rectangle it covers.
    
     A rectangle has the following parameters :
     
        - R - Number of rows
 
        - M(R_i) - Number of multiplications in row `i` corresponding to a certain kernel/co-kernel pair.

        - C - Number of Columns

        - M(C_j) - Number of multiplications in Column `j`. This actually corresponds to no. of multiplications in a kernel cube
          denoted by column `j`.

        Each element (i, j) represents a product term equal to the product
        of co-kernel `i` and kernel cube `j`. This element requires a total
        number of M(R_i) + M(C_j) + 1 multiplications to be computed.

        Therefore the total number of multiplications represented by the
        whole rectangle is equal to :

        R*sum(M(C_j)) + C*sum(M(C_j)) + R * C

        Each kernel corresponds to C - 1 addition operations.
        Therefore , total number of addition operations for a given
        rectangle : R * (C - 1)

        When this rectangle is selected, a common factor corresponding to
        sum(M(C_j)) multiplications and C - 1 additions is extracted.

        This common factor is multiplied by each row, which leads to a
        further sum(M(R_i)) + R multiplications.

        Therefore , the value of the rectangle is calculated as :
        
        value = m *{(C - 1)*(R + sum(M(R_i)) + (R - 1)*(sum(M(C_j))) + (R - 1)*(C - 1))
        
        best described as the weighted sum of the
        savings in the number of multiplications and additions.
* **Cube Literal Incidence Matrix(CIM)**

    Used in detection of single-cube common sub-expressions.
    
    Once again , we find the rectangle that yields a maximum-valued covering of the CIM. A similar algorithm that was used for KCM
    will be used again.

    The common cube corresponding to the prime rectangle is obtained by finding the minimum value in each column of the rectangle.

    Calculating the value of the rectangle :
    
         * sum(C[i]) -> Sum of integer powers in the extracted cube `C`.

         * Now, this cube saves sum(C[i]) - 1 multiplications in each
           row of the rectangle.

         * Therefore , the value of the rectangle : (R - 1)(sum(C[i]) - 1)

### Implementation Details :
-------------------------
The pseudocode for the following algorithms can be found in the mentioned paper above[[1]](http://cseweb.ucsd.edu/~kastner/papers/tech-poly_factorization_cse.pdf).


 * **Kernelling Algorithm : Generate set of kernels and corresponding co-kernels.**
                          
                            This is performed to construct the KCM , as mentioned earlier.

 * **Distill Algorithm : Multiple-Cube decomposition and factorization.**
        
                       This is a greedy iterative algorithm in which the best prime rectangle is extracted in each iteration :
                       
                       Outer loop : Kernels and co-kernels are extracted from set of expressions {p_i}, and KCM is formed
                                    formed from that using the Kernelling Algorithm.This outer loop exits if there is no favourable
                                    rectangle in the KCM.
                        
                       Inner loop : Each iteration in the inner loop selects the most valuable rectangle, if present based on the
                                    previously defined value function.
                                    This rectangle is now added to the set of expressions, and we denote this new expression by a 
                                    new literal(symbol in SymPy).

 * **Condense Algorithm : Single-Cube decomposition.**
                        
                        This is done after the Distill algorithm. In a CIM, single-term common subexpressions appear as rectangles
                        where the rectangle entries are non-zero integers.
                        
                        The CIM is updated by subtracting the extracted cube from all rows in the CIM in which it is contained.This 
                        basically extracts the best prime rectangle as decided by the value function described earlier. When no more 
                        favourable rectangles exist the algorithm is done.
                        
                        After this, a new literal(symbol) is added with the extracted value(this value corresponds to the prime rectangle)
                        to the existing literal set.

#### Phase I : Implementing a robust CSE
 * I will start off by implementing classes for KCM and CIM data structures :
 
           Prototype for KCM/CIM: 
           ```python
           #Insert prototype here.
           ```
   
 * Next are the helper functions :  
    * `Divide`, `Merge` - Required for Kernel and co-kernel extraction.
    * `Collapse` - Required for finding cube intersections.
    
    Implement and test them with some example polynomials.
 
 * Complete implementing code for : 
    * Kernel/co-Kernel extraction.
    * Kernel intersections.
    * Cube intersections.
 
 * Write unit tests for CSE.
 
#### Phase II : Imporve codegen dependencies 

### Timeline :
-----------
**Week 1 : **
 
 * Implement classes for KCM and CIM.

**Week 2: **
 
 * Implement and test helper functions : `Divide`, `Merge`, `Collapse`.Start code for kernel/co-kernel extraction.

**Week 3: **
 
 * Finish implementation of Kernel/co-Kernel extraction and Kernel intersections.

**Week 4: **
 
 * Finish code for cube intersections and unit tests. Make sure CSE implemented is robust and all unit tests pass.

**Week 5,6,7,8,9,10,11,12** 

### How do I fit in ?
-------------
### References :
------------------
1. [Optimizing	Polynomial	Expressions	by
Algebraic	Factorization	and	Common
Subexpression	Elimination - A.Hosangadi, F.Fallah, R.Kastner](http://cseweb.ucsd.edu/~kastner/papers/tech-poly_factorization_cse.pdf)

2. [Code Generation Notes](https://github.com/sympy/sympy/wiki/Code-Generation-Notes)

3. [EUROCAL '87](https://books.google.co.in/books?id=mB_9g2c29ikC&pg=PA66&dq=EUROCAL+%2787:+European+Conference+on+Computer+Algebra,+Leipzig+Page+66&hl=en&sa=X&ved=0ahUKEwjTyMjYxt_SAhUMpo8KHZhcBOEQ6AEIGzAA#v=onepage&q&f=false)