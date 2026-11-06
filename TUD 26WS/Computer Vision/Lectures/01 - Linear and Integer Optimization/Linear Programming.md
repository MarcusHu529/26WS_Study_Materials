## Linear Programming Fundamentals
### What is Linear Programming?
**Linear Programming (LP)** finds the best values of decision variables while satisfying linear constraints.
An LP has three main components:

| Component          | Purpose                              | Example          |     |
| ------------------ | ------------------------------------ | ---------------- | --- |
| Decision variables | What can we control?                 | $x_1,x_2$        |     |
| Objective function | What do we minimize or maximize?     | $\min 2x_1+3x_2$ |     |
| Constraints        | What restrictions must be satisfied? | $2x_1+x_2\geq4$  |     |
The general mathematical expression is:$$\boxed{\min_{x\in\mathbb R^n}c^Tx}$$subject to linear constraints.
**Important notation**
- $x\in\mathbb R^n$: Vector of \(n\) real-valued decision variables.
- $c\in\mathbb R^n$: Cost vector containing the coefficients of the objective.
- $c^Tx$: Dot product, giving the total objective value.
- $A\in\mathbb R^{m\times n}$: Constraint matrix with \(m\) rows and \(n\) columns.
- $b\in\mathbb R^m$: Right-hand-side vector.
**For example:**$$c=\begin{pmatrix}2\\3\end{pmatrix}, \quad x=\begin{pmatrix}x_1\\x_2\end{pmatrix}$$
Then:$$ c^Tx=(2,3)\begin{pmatrix}x_1\\x_2\end{pmatrix}=2x_1+3x_2 $$
### Feasible vs. Optimal Solutions
A **feasible solution** satisfies all constraints.
A **feasible set** contains all feasible solutions:$$\boxed{X_A^b=\{x\in\mathbb R^n\mid Ax=b\land x\geq0\}}$$
This notation is specifically for a standard-form system.
An **optimal solution** $x^*$ is a feasible solution with the smallest objective value for a minimization LP. $$\boxed{c^Tx^*\leq c^Tx\quad\forall x\text{ feasible}}$$
Distinguish these two quantities:$$ \underbrace{x^*}_{\text{Optimal decision vector}} \qquad \underbrace{z^*=c^Tx^*}_{\text{Optimal objective value}} $$
For example, if $x^*=(1,2)^T$ and $c=(2,3)^T$:$$ z^*=2(1)+3(2)=8 $$
The optimal solution is a vector; the optimal cost is a number.
### General, Canonical, and Standard Forms
| Form      | Linear constraints      | Variable restrictions              |
| --------- | ----------------------- | ---------------------------------- |
| General   | $Ax=b,\quad A'x\geq b'$ | Some variables may be unrestricted |
| Canonical | $Ax\geq b$              | All variables $\geq0$              |
| Standard  | $Ax=b$                  | All variables $\geq0$              |
A standard-form LP is written: $$ \boxed{ \begin{aligned} \min\quad&c^Tx\\ \text{subject to}\quad&Ax=b\\ &x\geq0 \end{aligned}} $$
### Rules for converting to standard form
**Rule A — Unrestricted variable**
If x can be positive or negative:$$ \boxed{x=x^+-x^-} $$ with:$$ x^+,x^-\geq0 $$
Both new variables are nonnegative, but their difference can be any real number.
**Rule B — Greater-than-or-equal inequality**
For:$$ 2x+y\geq5 $$subtract a nonnegative surplus variable:$$ \boxed{2x+y-s=5,\qquad s\geq0}$$
**Rule C — Less-than-or-equal inequality**
For:$$2x+y\leq5$$add a nonnegative slack variable: $$\boxed{2x+y+s=5,\qquad s\geq0}$$
**Rule D — Equality**
Keep the equality unchanged, except for any required variable substitutions.
**Rule E — Convert an equality into canonical inequalities**
$$ \boxed{ ax=b\iff \begin{cases} ax\geq b\\ -ax\geq-b \end{cases}} $$
**Rule F — Maximization to minimization** $$ \max f(x)\iff\min -f(x) $$
The optimizing decisions are the same, but the optimal objective value changes sign.
### Worked conversion example
Consider: $$\begin{aligned} \min\quad&2x+3y\\ \text{subject to}\quad &x-2y\leq3\\ &2x+y=4\\ &y\geq0 \end{aligned}$$
where x is unrestricted.
Replace: $$x=x^+-x^- $$
Introduce slack variable $s\geq0$ for the first inequality.
The complete standard form becomes:$$\boxed{ \begin{aligned} \min\quad&2x^+-2x^-+3y\\ \text{subject to}\quad &x^+-x^--2y+s=3\\ &2x^+-2x^-+y=4\\ &x^+,x^-,y,s\geq0 \end{aligned}}$$
Define the decision vector and cost vector:$$\mathbf{x}= \begin{pmatrix}x^+\\x^-\\y\\s\end{pmatrix}, \qquad c=\begin{pmatrix}2\\-2\\3\\0\end{pmatrix}$$
The matrix representation is:$$A= \begin{pmatrix} 1&-1&-2&1\\ 2&-2&1&0 \end{pmatrix}, \qquad b=\begin{pmatrix}3\\4\end{pmatrix}$$Therefore:$$ A\in\mathbb R^{2\times4} $$Here, m=2 equality constraints and n=4 variables.
## Bases and Basic Feasible Solutions
### What is a Basis?
For the standard system:$$Ax=b,\qquad x\geq0$$suppose:$$A\in\mathbb R^{m\times n}, \quad \operatorname{rank}(A)=m$$
A basis K selects exactly m linearly independent columns of A.
The selected columns form a square, invertible matrix $A_{\cdot K}$.
- Basic variables: Variables whose indices belong to K.
- Nonbasic variables: Variables whose indices do not belong to K.
- Every nonbasic variable is set to zero.
For example, if \(m=2,n=4\):$$K=\{1,3\}$$then $x_1,x_3$ are basic, and:$$x_2=x_4=0$$
### Basic feasible solution (BFS)
The lecturer defines a BFS using: $$ \boxed{ \begin{aligned} x_K&=A_{\cdot K}^{-1}b\\ x_{\{1,\ldots,n\}\setminus K}&=0 \end{aligned}} $$
The calculated solution must also satisfy:$$ x\geq0$$
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


