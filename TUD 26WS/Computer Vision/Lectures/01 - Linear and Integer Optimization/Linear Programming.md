## Linear Programming Fundamentals
### What is Linear Programming?
**Linear Programming (LP)** finds the best values of decision variables while satisfying linear constraints.
An LP has three main components:

| Component          | Purpose                              | Example          |     |
| ------------------ | ------------------------------------ | ---------------- | --- |
| Decision variables | What can we control?                 | $x_1,x_2$        |     |
| Objective function | What do we minimize or maximize?     | $\min 2x_1+3x_2$ |     |
| Constraints        | What restrictions must be satisfied? | $2x_1+x_2\geq4$  |     |
The general mathematical expression is:

$$
\boxed{\min_{x\in\mathbb R^n}c^Tx}
$$

subject to linear constraints.
**Important notation**
- $x\in\mathbb R^n$: Vector of $n$ real-valued decision variables.
- $c\in\mathbb R^n$: Cost vector containing the coefficients of the objective.
- $c^Tx$: Dot product, giving the total objective value.
- $A\in\mathbb R^{m\times n}$: Constraint matrix with $m$ rows and $n$ columns.
- $b\in\mathbb R^m$: Right-hand-side vector.
**For example:**

$$
c=\begin{pmatrix}2\\3\end{pmatrix}, \quad x=\begin{pmatrix}x_1\\x_2\end{pmatrix}
$$

Then:

$$
c^Tx=(2,3)\begin{pmatrix}x_1\\x_2\end{pmatrix}=2x_1+3x_2
$$

### Feasible vs. Optimal Solutions
A **feasible solution** satisfies all constraints.
A **feasible set** contains all feasible solutions:

$$
\boxed{X_A^b=\{x\in\mathbb R^n\mid Ax=b\land x\geq0\}}
$$

This notation is specifically for a standard-form system.
An **optimal solution** $x^*$ is a feasible solution with the smallest objective value for a minimization LP. 

$$
\boxed{c^Tx^*\leq c^Tx\quad\forall x\text{ feasible}}
$$

Distinguish these two quantities:

$$
\underbrace{x^*}_{\text{Optimal decision vector}} \qquad \underbrace{z^*=c^Tx^*}_{\text{Optimal objective value}}
$$

For example, if $x^*=(1,2)^T$ and $c=(2,3)^T$:

$$
z^*=2(1)+3(2)=8
$$

The optimal solution is a vector; the optimal cost is a number.
### General, Canonical, and Standard Forms
| Form      | Linear constraints      | Variable restrictions              |
| --------- | ----------------------- | ---------------------------------- |
| General   | $Ax=b,\quad A'x\geq b'$ | Some variables may be unrestricted |
| Canonical | $Ax\geq b$              | All variables $\geq0$              |
| Standard  | $Ax=b$                  | All variables $\geq0$              |
A standard-form LP is written: 

$$
\boxed{ \begin{aligned} \min\quad&c^Tx\\ \text{subject to}\quad&Ax=b\\ &x\geq0 \end{aligned}}
$$

### Rules for converting to standard form
**Rule A — Unrestricted variable**
If x can be positive or negative:

$$
\boxed{x=x^+-x^-}
$$

 with:

$$
x^+,x^-\geq0
$$

Both new variables are nonnegative, but their difference can be any real number.
**Rule B — Greater-than-or-equal inequality**
For:

$$
2x+y\geq5
$$

subtract a nonnegative surplus variable:

$$
\boxed{2x+y-s=5,\qquad s\geq0}
$$

**Rule C — Less-than-or-equal inequality**
For:

$$
2x+y\leq5
$$

add a nonnegative slack variable: 

$$
\boxed{2x+y+s=5,\qquad s\geq0}
$$

**Rule D — Equality**
Keep the equality unchanged, except for any required variable substitutions.
**Rule E — Convert an equality into canonical inequalities**

$$
\boxed{ ax=b\iff \begin{cases} ax\geq b\\ -ax\geq-b \end{cases}}
$$

**Rule F — Maximization to minimization** 

$$
\max f(x)\iff\min -f(x)
$$

The optimizing decisions are the same, but the optimal objective value changes sign.
### Worked conversion example
Consider: 

$$
\begin{aligned} \min\quad&2x+3y\\ \text{subject to}\quad &x-2y\leq3\\ &2x+y=4\\ &y\geq0 \end{aligned}
$$

where x is unrestricted.
Replace: 

$$
x=x^+-x^-
$$

Introduce slack variable $s\geq0$ for the first inequality.
The complete standard form becomes:

$$
\boxed{ \begin{aligned} \min\quad&2x^+-2x^-+3y\\ \text{subject to}\quad &x^+-x^--2y+s=3\\ &2x^+-2x^-+y=4\\ &x^+,x^-,y,s\geq0 \end{aligned}}
$$

Define the decision vector and cost vector:

$$
\mathbf{x}= \begin{pmatrix}x^+\\x^-\\y\\s\end{pmatrix}, \qquad c=\begin{pmatrix}2\\-2\\3\\0\end{pmatrix}
$$

The matrix representation is:

$$
A= \begin{pmatrix} 1&-1&-2&1\\ 2&-2&1&0 \end{pmatrix}, \qquad b=\begin{pmatrix}3\\4\end{pmatrix}
$$

Therefore:

$$
A\in\mathbb R^{2\times4}
$$

Here, m=2 equality constraints and n=4 variables.
## Bases and Basic Feasible Solutions
### What is a Basis?
For the standard system:

$$
Ax=b,\qquad x\geq0
$$

suppose:

$$
A\in\mathbb R^{m\times n}, \quad \operatorname{rank}(A)=m
$$

A basis K selects exactly m linearly independent columns of A.
The selected columns form a square, invertible matrix $A_{\cdot K}$.
- Basic variables: Variables whose indices belong to K.
- Nonbasic variables: Variables whose indices do not belong to K.
- Every nonbasic variable is set to zero.
For example, if $m=2,n=4$:

$$
K=\{1,3\}
$$

then $x_1,x_3$ are basic, and:

$$
x_2=x_4=0
$$

### Basic feasible solution (BFS)
The lecturer defines a BFS using: 

$$
\boxed{ \begin{aligned} x_K&=A_{\cdot K}^{-1}b\\ x_{\{1,\ldots,n\}\setminus K}&=0 \end{aligned}}
$$

The calculated solution must also satisfy:

$$
x\geq0
$$

**Important distinction:**

|Term|Meaning|
|---|---|
|Basic solution|Obtained using a valid basis, with nonbasic variables zero|
|Basic feasible solution|A basic solution that also satisfies all constraints, including non-negativity|
|Feasible but not basic|Satisfies constraints but cannot be represented as basic for any valid basis|

A BFS may have fewer than m strictly positive variables. This is possible in a degenerate solution.

Theorem 1 (Lecture 2, slide 5): Assuming A has full row rank, every nonempty standard-form feasible set contains at least one BFS.

The proof's main idea is that if the columns associated with the nonzero components of a feasible solution are linearly dependent, we can move along a dependence direction while preserving feasibility until at least one additional variable becomes zero. Repeating this eventually gives a BFS.
## One Complete Worked Example

Connects LP formulation, standard form, BFS, relative cost, pivoting, optimality, and ILP relaxation.

We'll use the **farm feed problem from Lecture 1**, and then apply the solution methods from Lecture 2.

### Step 1 — Formulate the real-world problem

A farmer purchases two types of feed.

| Nutrient or cost | Feed 1 | Feed 2 | Minimum required |
|--------|--------|--------|--------|
| Protein | 200 | 100 | 400 |
| Fibre | 100 | 300 | 600 |
| Cost per kg | €2 | €3 | — |

Let:

$$
x_1=\text{kg of Feed 1}
$$

$$
x_2=\text{kg of Feed 2}
$$

We want to minimize the total cost:

$$
\min 2x_1+3x_2
$$

Constraints:

$$
\begin{aligned}
200x_1+100x_2&\geq400\\
100x_1+300x_2&\geq600\\
x_1,x_2&\geq0
\end{aligned}
$$

We can divide both nutrient constraints by 100 without changing the feasible solutions:

$$
\boxed{
\begin{aligned}
\min\quad&2x_1+3x_2\\
\text{subject to}\quad
&2x_1+x_2\geq4\\
&x_1+3x_2\geq6\\
&x_1,x_2\geq0
\end{aligned}}
$$

### Step 2 — Convert to standard form

Both nutrient constraints are $\geq$ inequalities.

Introduce two surplus variables:

$$
s_1,s_2\geq0
$$

Subtract the surplus variables:

$$
\boxed{
\begin{aligned}
\min\quad&2x_1+3x_2\\
\text{subject to}\quad
&2x_1+x_2-s_1=4\\
&x_1+3x_2-s_2=6\\
&x_1,x_2,s_1,s_2\geq0
\end{aligned}}
$$

We now have four variables and two equality constraints.

### Step 3 — Construct the matrix representation

Define:

$$
x=
\begin{pmatrix}
x_1\\x_2\\s_1\\s_2
\end{pmatrix}
$$

The matrix representation is:

$$
\boxed{
A=
\begin{pmatrix}
2&1&-1&0\\
1&3&0&-1
\end{pmatrix}}
$$

$$
b=\begin{pmatrix}4\\6\end{pmatrix},
\qquad
c=\begin{pmatrix}2\\3\\0\\0\end{pmatrix}
$$

Thus:

$$
A\in\mathbb R^{2\times4}
$$

and:

$$
\min c^Tx\quad\text{subject to }Ax=b,\ x\geq0
$$

### Step 4 — Find an initial BFS

We need two basic variables because $m=2$.

Choose:

$$
\boxed{K=\{2,4\}}
$$

This selects $x_2$ and $s_2$ as basic variables.

The nonbasic variables are set to zero:

$$
x_1=0,\qquad s_1=0
$$

Substitute into the constraints.

First constraint:

$$
2(0)+x_2-0=4
$$

Therefore:

$$
x_2=4
$$

Second constraint:

$$
0+3(4)-s_2=6
$$

$$
12-s_2=6
$$

$$
s_2=6
$$

Our initial solution is:

$$
\boxed{x^0=(0,4,0,6)^T}
$$

This is a BFS because $K$ selects two independent columns, $Ax^0=b$, and all components are nonnegative.

The objective cost is:

$$
\begin{aligned}
C^0&=2x_1+3x_2\\
&=2(0)+3(4)\\
&=\boxed{12}
\end{aligned}
$$

**Interpretation:** Buying 4 kg of Feed 2 satisfies the nutrient requirements at a cost of €12.

But is this optimal? We don't know yet.

### Step 5 — Choose an entering variable

Our nonbasic variables are:

$$
x_1=0,\qquad s_1=0
$$

We will test whether increasing $x_1$ improves the objective.

The lecturer's relative-cost formula is:

$$
\boxed{c'_{k^*}=c_{k^*}-\sum_{k\in K}c_k y_k^{k^*}}
$$

First, we need to calculate $y^1$, which describes how the current basic variables change when $x_1$ increases.

The current basis matrix selects columns 2 and 4:

$$
A_{\cdot K}=
\begin{pmatrix}
1&0\\
3&-1
\end{pmatrix}
$$

The column corresponding to entering variable $x_1$ is:

$$
A_{\cdot1}=\begin{pmatrix}2\\1\end{pmatrix}
$$

We solve:

$$
A_{\cdot K}y_K^1=A_{\cdot1}
$$

That is:

$$
\begin{pmatrix}
1&0\\
3&-1
\end{pmatrix}
\begin{pmatrix}
y_2^1\\y_4^1
\end{pmatrix}
=
\begin{pmatrix}2\\1\end{pmatrix}
$$

The equations are:

$$
\begin{aligned}
y_2^1&=2\\
3y_2^1-y_4^1&=1
\end{aligned}
$$

Substitute $y_2^1=2$:

$$
3(2)-y_4^1=1
$$

$$
\boxed{y_4^1=5}
$$

Therefore:

$$
\boxed{y_K^1=(2,5)^T}
$$

**Interpretation:** Increasing $x_1$ by one unit decreases $x_2$ by 2 units and $s_2$ by 5 units, while preserving both equality constraints.

### Step 6 — Calculate relative cost

We have:

$$
c_1=2,\quad c_2=3,\quad c_4=0
$$

And:

$$
y_2^1=2,\qquad y_4^1=5
$$

Substitute into the relative-cost formula:

$$
\begin{aligned}
c'_1&=c_1-(c_2y_2^1+c_4y_4^1)\\
&=2-(3\cdot2+0\cdot5)\\
&=2-6\\
&=\boxed{-4}
\end{aligned}
$$

Since:

$$
c'_1<0
$$

increasing $x_1$ can decrease the objective.

**We select $x_1$ to enter the basis.**

### Step 7 — Determine the maximum feasible pivot step

Let:

$$
x_1=\lambda
$$

Adjust the basic variables to preserve the constraints.

From the original equations:

$$
\begin{aligned}
2\lambda+x_2&=4\\
\lambda+3x_2-s_2&=6
\end{aligned}
$$

Rearrange the first equation:

$$
x_2=4-2\lambda
$$

Substitute into the second:

$$
\lambda+3(4-2\lambda)-s_2=6
$$

$$
\lambda+12-6\lambda-s_2=6
$$

$$
s_2=6-5\lambda
$$

Thus:

$$
\boxed{
\begin{aligned}
x_1&=\lambda\\
x_2&=4-2\lambda\\
s_1&=0\\
s_2&=6-5\lambda
\end{aligned}}
$$

To maintain feasibility:

$$
x_2=4-2\lambda\geq0
$$

Therefore:

$$
\lambda\leq2
$$

And:

$$
s_2=6-5\lambda\geq0
$$

Therefore:

$$
\lambda\leq\frac65
$$

The minimum-ratio test gives:

$$
\boxed{\lambda'=\min\left(\frac42,\frac65\right)=\frac65=1.2}
$$

At this step, $s_2$ reaches zero first.

Thus:

- $x_1$ enters the basis.
- $s_2$ leaves the basis.

The new basis is:

$$
\boxed{K'=\{1,2\}}
$$

### Step 8 — Calculate the new BFS

Substitute $\lambda'=6/5$:

$$
x_1=\frac65=1.2
$$

$$
x_2=4-2\left(\frac65\right)
=\frac85=1.6
$$

$$
s_1=0
$$

$$
s_2=6-5\left(\frac65\right)=0
$$

Our new BFS is:

$$
\boxed{x^1=(1.2,1.6,0,0)^T}
$$

Check feasibility:

First constraint:

$$
2(1.2)+1.6=4
$$

Second constraint:

$$
1.2+3(1.6)=6
$$

Both constraints are satisfied, and every variable is nonnegative.

### Step 9 — Calculate the new objective cost

The new cost is:

$$
\begin{aligned}
C^1&=2(1.2)+3(1.6)\\
&=2.4+4.8\\
&=\boxed{7.2}
\end{aligned}
$$

Compare:

$$
C^0=12,\qquad C^1=7.2
$$

The cost reduction is:

$$
\Delta C=7.2-12=\boxed{-4.8}
$$

Verify using relative cost:

$$
\boxed{\Delta C=c'_1\lambda'}
$$

$$
=(-4)(1.2)=-4.8
$$

Both calculations agree.

### Step 10 — Check optimality

We have reached a better BFS, but we must determine whether it is optimal.

Our new basis is:

$$
K'=\{1,2\}
$$

Therefore, $x_1$ and $x_2$ are basic, while $s_1$ and $s_2$ are nonbasic.

The basis matrix is:

$$
A_{\cdot K'}=
\begin{pmatrix}
2&1\\
1&3
\end{pmatrix}
$$

Its inverse is:

$$
A_{\cdot K'}^{-1}
=
\frac15
\begin{pmatrix}
3&-1\\
-1&2
\end{pmatrix}
$$

We now calculate the relative costs of both nonbasic variables.

**For $s_1$:**

$$
A_{\cdot3}=\begin{pmatrix}-1\\0\end{pmatrix}
$$

$$
y_K^3=A_{\cdot K'}^{-1}A_{\cdot3}
=\begin{pmatrix}-3/5\\1/5\end{pmatrix}
$$

Therefore:

$$
\begin{aligned}
c'_3&=0-\left(2\left(-\frac35\right)+3\left(\frac15\right)\right)\\
&=\boxed{\frac35}
\end{aligned}
$$

**For $s_2$:**

$$
A_{\cdot4}=\begin{pmatrix}0\\-1\end{pmatrix}
$$

$$
y_K^4=A_{\cdot K'}^{-1}A_{\cdot4}
=\begin{pmatrix}1/5\\-2/5\end{pmatrix}
$$

Therefore:

$$
\begin{aligned}
c'_4&=0-\left(2\left(\frac15\right)+3\left(-\frac25\right)\right)\\
&=\boxed{\frac45}
\end{aligned}
$$

Both relative costs are positive. The basic variables have zero relative cost.

Our final relative-cost vector is:

$$
\boxed{c'=\left(0,0,\frac35,\frac45\right)^T}
$$

Every component is nonnegative.

According to the lecturer's optimality theorem:

$$
\boxed{c'_k\geq0\ \forall k\implies x\text{ is optimal}}
$$

Therefore, the current BFS is optimal.

**Final result:** Feed 1: 1.2 kg; Feed 2: 1.6 kg; minimum cost: €7.20.

We started with a feasible purchase costing €12, performed one simplex pivot, and proved that €7.20 is the minimum cost.

---

## Simplex Algorithm: Essential Formulas

LECTURE 2 · SLIDES 8–19

### Pivoting

Choose an entering variable $k^*$ not currently in the basis.

Calculate its effect on the basic variables:

$$
\boxed{y_K^{k^*}=A_{\cdot K}^{-1}A_{\cdot k^*}}
$$

Then:

$$
\boxed{x'_K=x_K-\lambda y_K^{k^*}}
$$

and:

$$
x'_{k^*}=\lambda
$$

The ratio test is:

$$
\boxed{
\lambda'=
\min_{\substack{k\in K\\y_k^{k^*}>0}}
\frac{x_k}{y_k^{k^*}}
}
$$

Only positive $y_k^{k^*}$ values limit the increase, because those basic variables decrease as $\lambda$ increases.

If no such index exists and the relative cost is negative, the objective is unbounded below.

### Relative cost and objective change

$$
\boxed{c'_{k^*}=c_{k^*}-\sum_{k\in K}c_ky_k^{k^*}}
$$

$$
\boxed{\Delta C=c'_{k^*}\lambda'}
$$

For minimization:

| Relative cost | Interpretation |
|--------|--------|
| Negative | Potential cost-reducing direction |
| Zero | No cost change along that direction |
| Positive | Cost-increasing direction |

A negative relative cost is useful only when a positive feasible movement is possible.

### Optimality, degeneracy, and unboundedness

**Optimality theorem**

If the current BFS satisfies:

$$
\boxed{c'_k\geq0\quad\forall k}
$$

then it is globally optimal.

**Degenerate pivot**

If:

$$
\lambda'=0
$$

then:

$$
\Delta C=c'_{k^*}(0)=0
$$

The basis can change without changing the objective value. Consequently, a negative relative cost does not by itself prove that the current BFS is nonoptimal.

**Unbounded objective**

If:

$$
c'_{k^*}<0
$$

and $y_k^{k^*}\leq0$ for every basic variable $k$, the entering variable can increase indefinitely while preserving feasibility.

Then:

$$
C(\lambda)=C(0)+c'_{k^*}\lambda\rightarrow-\infty
$$

The LP has no finite minimum.

### The complete simplex algorithm

1. Start with a BFS $x^0$ and basis $K$.
2. Calculate all relative costs.
3. If every relative cost is nonnegative, stop: the current BFS is optimal.
4. Otherwise, choose a nonbasic entering variable with $c'_{k^*}<0$.
5. Calculate $y_K^{k^*}=A_{\cdot K}^{-1}A_{\cdot k^*}$.
6. If all its basic entries are nonpositive, stop: the objective is unbounded below.
7. Otherwise, use the minimum-ratio test to choose the leaving variable, pivot, and return to step 2.

The lecturer notes that simplex works well in practice but has exponential worst-case complexity. Other polynomial-time LP algorithms exist.

### Three pivoting rules

| Rule | Selection criterion | Advantage |
|--------|--------|--------|
| Most negative relative cost | Minimize $c'_k$ | Simple |
| Greatest cost reduction | Minimize $c'_k\lambda'_k$ | Largest immediate improvement |
| Bland's Rule | Smallest eligible variable index | Prevents cycling |

Bland's Rule also chooses the smallest-index leaving variable when the minimum-ratio test produces a tie.

For example:

| Candidate | $c'_k$ | $\lambda'_k$ | $\Delta C$ |
|--------|--------|--------|--------|
| $x_2$ | −4 | 1 | −4 |
| $x_3$ | −2 | 5 | −10 |
| $x_5$ | −6 | 0.5 | −3 |

Then:

- Rule 1 chooses $x_5$.
- Rule 2 chooses $x_3$.
- Bland's Rule chooses $x_2$.

The different choices illustrate why relative cost, actual cost reduction, and cycling prevention are separate concerns.

---

## Integer Linear Programming and LP Relaxation

LECTURE 2 · SLIDE 20

### ILP vs. LP

An Integer Linear Program requires integer-valued decision variables:

$$
\boxed{z_{\mathrm{ILP}}=\min\{c^Tx\mid Ax=b,\ x\geq0,\ x\in\mathbb Z^n\}}
$$

LP relaxation removes the integrality restriction:

$$
\boxed{z_{\mathrm{LP}}=\min\{c^Tx\mid Ax=b,\ x\geq0,\ x\in\mathbb R^n\}}
$$

For a minimization problem:

$$
\boxed{z_{\mathrm{LP}}\leq z_{\mathrm{ILP}}}
$$

This follows because every feasible ILP solution is feasible for the LP relaxation, but not vice versa.

### Continue the farm example with integrality

Suppose the farmer must buy feed in whole-kilogram packages.

Now:

$$
x_1,x_2\in\mathbb Z_{\geq0}
$$

We already calculated the LP relaxation optimum:

$$
x_{\mathrm{LP}}^*=(1.2,1.6)^T
$$

with:

$$
\boxed{z_{\mathrm{LP}}=7.2}
$$

But fractional kilograms are no longer allowed.

Try purchasing:

$$
x_1=1,\qquad x_2=2
$$

Check constraints:

$$
2(1)+2=4\geq4
$$

$$
1+3(2)=7\geq6
$$

The solution is integer-feasible.

Calculate cost:

$$
C=2(1)+3(2)=\boxed{8}
$$

We know:

$$
z_{\mathrm{ILP}}\geq z_{\mathrm{LP}}=7.2
$$

Since all costs are integer-valued when $x_1,x_2$ are integers and the cost coefficients are integers, any integer-feasible objective must be at least 8.

We have already found an integer-feasible solution costing exactly 8.

Therefore:

$$
\boxed{z_{\mathrm{ILP}}=8}
$$

Our final comparison:

|  | LP relaxation | ILP |
|--------|--------|--------|
| Feed 1 | 1.2 kg | 1 kg |
| Feed 2 | 1.6 kg | 2 kg |
| Optimal cost | €7.20 | €8.00 |

**Why this matters:** LP relaxation can give a useful bound on the best integer solution even when solving an ILP is computationally difficult. The lecturer notes that ILP is NP-hard in general.

---

## Mathematical Notation Cheat Sheet

These are the symbols most likely to cause confusion when reading the lecturer's slides.

| Symbol | How to read it / meaning |
|--------|--------|
| $x\in\mathbb R^n$ | $x$ is a real-valued vector of length $n$ |
| $A\in\mathbb R^{m\times n}$ | $A$ is a matrix with $m$ rows and $n$ columns |
| $\operatorname{rank}(A)=m$ | $A$ has $m$ linearly independent rows |
| $c^Tx$ | Cost-vector transpose times decision vector |
| $x^*$ | An optimal decision vector |
| $z^*$ | Optimal objective value |
| $K$ | Indices of basic variables |
| $A_{\cdot K}$ | Columns of $A$ selected by basis $K$ |
| $A_{\cdot K}^{-1}$ | Inverse of the selected basis matrix |
| $k^*$ | Entering variable's index |
| $k^\dagger$ | Leaving variable's index |
| $y_k^{k^*}$ | Direction coefficient for basic variable $k$ when $k^*$ enters |
| $\lambda'$ | Maximum feasible pivot step |
| $c'_k$ | Relative cost of variable $k$ |
| $\Delta C$ | Change in objective cost |
| $\forall$ | For every |
| $\land$ | Logical AND |
| $\setminus$ | Set difference: elements in the first set but not in the second |

## What to Remember for the Examination

| Topic          | Essential understanding                                            |
| -------------- | ------------------------------------------------------------------ |
| LP formulation | Identify decision variables, objective, and constraints            |
| Standard form  | Equalities only, all variables nonnegative                         |
| Basis          | Select m linearly independent columns                              |
| BFS            | Valid basis, nonbasic variables zero, solution feasible            |
| Pivoting       | Exchange one basic and one nonbasic variable                       |
| Ratio test     | Prevent any variable becoming negative                             |
| Relative cost  | Determines how the objective changes as a variable enters          |
| Optimality     | All relative costs nonnegative certify the current BFS as optimal  |
| Unboundedness  | Negative relative cost with unlimited feasible step                |
| Degeneracy     | Zero pivot step can change the basis without improving cost        |
| Bland's Rule   | Prevent cycling                                                    |
| LP relaxation  | Removes integrality and gives a lower bound for a minimization ILP |
### ### The five formulas worth memorizing and understanding

1 · STANDARD-FORM LP

$$ \min c^Tx,\quad Ax=b,\quad x\geq0 $$

2 · BASIC FEASIBLE SOLUTION

$$ x_K=A_{\cdot K}^{-1}b,\quad x_{\bar K}=0,\quad x\geq0 $$

Here \(\bar K=\{1,\ldots,n\}\setminus K\).

3 · MINIMUM-RATIO TEST

$$ \lambda'=\min_{k:y_k^{k^*}>0}\frac{x_k}{y_k^{k^*}} $$

The minimum is over eligible basic variables.

4 · RELATIVE COST AND COST CHANGE

$$ c'_{k^*}=c_{k^*}-\sum_{k\in K}c_ky_k^{k^*} $$

$$ \Delta C=c'_{k^*}\lambda' $$

5 · OPTIMALITY AND LP RELAXATION

$$\forall k:\ c'_k\geq0\implies x\text{ optimal} $$

$$z_{\mathrm{LP}}\leq z_{\mathrm{ILP}}$$


