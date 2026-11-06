## Questions
1. For rule A conversion, is x+y can only be 10 even after the conversion?
## Understanding Linear Optimization
- A linear optimization problem consists of three essential components:
	1. Decision variables - What can we choose?
	2. Objective function - What do we want to achieve?
	3. Constraints - What rules must be obeyed?
- Choose decision variables to minimize or maximize a linear objective while satisfying constraints. $$\min c^Tx$$
	- $x$: Decision-variable vector
	- $c$: Cost-coefficient vector
	- $c^Tx$: Weighted sum of the decision variables
	- Constraints: Equations or inequalities restricting the choices

- **Example**$$\boxed{ \begin{aligned} \min_{x_1,x_2,x_3,x_4\in\mathbb R}\quad &x_1-3x_2+2x_3\\ \text{subject to}\quad &x_1+x_2\geq2\\ &3x_2-x_3+x_4=5\\ &x_3+x_4\leq3\\ &x_1\geq0\\ &x_2\leq0 \end{aligned} }$$
- **Feasible solution vs. Optimal solution
	- Feasible: Satisfies every constraint.
	- Optimal: Feasible and achieves the best objective value.
	- Optimal cost: Objective value at an optimum.
- **Maximization vs. Minimization**
$$\boxed{\max f(x)\quad\Longleftrightarrow\quad\min[-f(x)]}$$
## The Three Forms

| Form      | Allowed constraint types             | Sign requiremnets                                                                                        |
| --------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------- |
| General   | Equations $=$ and inequalities $\ge$ | Only selected variables must be non-negative                                                             |
| Canonical | Inequalities $\ge$                   | Every linear constraint uses greater-than-or-equal-to, and every decision variable must be non-negative. |
| Standard  | Equations =                          | Every linear constraint is an equation, and every variable must be non-negative.                         |
## Convertions
**Rule A: Convert an equality into inequalities**
- Suppose: $x+y=10$
- Equality means two things at once:
	- $x+y\ge10$
	- $x+y\le10$
- To make both use $\ge$
	- $-x-y\ge-10$
- Thus: $x+y=10 \iff \begin{cases} x+y\geq10\\ -x-y\geq-10 \end{cases}$
**Rule B: Convert an inequality into an equality**
- Suppose: $x+y\ge10$
- The sum can exceed 10
- Add a new non-negative variable $s$:
	- $x+y-s=10,\quad s\geq0$
	- For $x+y=13$, choose $s=3$
- $s$ measures how much the left side exceeds the required minimum, which is called a **Surplus variable**.
- If $x+y\le10$ can beome $x+y+s=10$ with $s\ge0$. s is called a **Slack variable**.
**Rule C: Replace a varibale that can be negative**
- Suppose $x$ is unrestricted in sign.
	- $x=x^+-x^-$   $x^+,x^-\geq0$
	- If original $x$ is positive
		- Choose $x^+$
	- If negative
		- Choose $x^-$
## Conversion practice
$$\begin{aligned}x+y+z&=10\\2x+z&=0\\x,y,z&\geq0\end{aligned}$$
(a) What form is it?
- Both constraints use equality, and all variables are non-negative. Therefore, it is in standard form.
(b) Convert it into Canonical form
$$\begin{aligned}x+y+z&\ge10\\-x-y-z&\ge-10\\2x+z&\ge 0\\-2x-z&\ge 0\\x,y,z&\geq0\end{aligned}$$
- Formula: ![[Pasted image 20261018155839.png]]

$$\begin{aligned} x+y&\geq5\\ -x+2y&\geq-3\\ 3x+2y&=12\\ y&\geq0 \end{aligned}$$
Note: x has no sign restriction.
(a) What form is it?
- x has no sign restriction and there are equalities and inequalities. Therefore, it is a general form.
(b) Convert it into Canonical form
- Use rule C for x, because it has no restriction in sign.
$$\begin{aligned} x=x^+ &- x^-\\ x^+,x^-&\geq0\\ x^+ -x^- +y &\ge 5\\  -x^+ +x^- +2y &\ge -3 \\ 3x^+ -3x^- +2y &\ge 12\\ -3x^+ +3x^- -2y &\ge -12 \\x^+ , x^-,y&\ge 0\end{aligned}$$
(c) Convert it into Standard form
$$\begin{aligned} x=x^+ &- x^-\\x^+ - x^- +y -s_{1}&=5 \\-x^+ +x^- +2y-s_{2}&=-3\\3x^+ -3x^- +2y&=12\\ x^+,x^-,y,s_{1},s_{2}&\ge 0\end{aligned}$$
