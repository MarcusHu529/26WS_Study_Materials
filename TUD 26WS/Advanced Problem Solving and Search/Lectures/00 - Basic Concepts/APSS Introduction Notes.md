## Why Are Problems Difficult to Sovle?
- **Large search space:** Too many possible solutions to examine using brute force.
- **Complex modeling:** Simplified models may not accurately represent reality.
- **Changing conditions:** The problem or its evaluation function may change over time.
- **Constraints:** Finding a feasible solution can be difficult, even before optimization.
## Search Space
**Definition:** The set of all possible candidate solutions to a problem.
**SAT (Boolean Satisfiability Problem)**
- **Goal:** Find an assignment of Boolean variables that makes a formula **True**.
- Each variable has two choices: **True** or **False**.
- For $n$ variables:
$$|S|=2^n$$
**TSP (Traveling Salesperson Problem)**
- **Goal:** Visit every city exactly once, return to the starting city, and minimize total distance.
- For $n$ cities (symmetric TSP):
$$|S|=\frac{(n-1)!}{2}$$
**Key takeaways**: Search spaces grow exponentially or factorially, making exhaustive search impractical for large problems.
## Evaluation Function
**Definition**: A function used to measure the quality of a candidate solution.
- SAT: A TRUE/FALSE result tells us whether an assignment satifies the formula, but provides little guidance if it fails.
- TSP: Total travel distance gives a numerical cost of comparing tours.
**Key takeaways**: A useful evaluation function helps distinguish bettwe candidates from worse ones and can guide the search.
## Problem Modeling
$$Problem \to Model \to Solution$$
**Approach 1:** Approximate Model + Precise Solution
You simplify the real-world problem to make it easier to solve.
**For example:**
- Your model considers only road distances.
- It ignores traffic, weather, and road closures.
- You use an algorithm to find the shortest possible route according to that model.
**Result:** You have found the mathematically optimal solution, but it might not be the fastest route in reality.

**Approach 2:** Precise Model + Approximate Solution
You create a more realistic model, but accept a solution that may not be mathematically optimal.
**For example:**
- Your model includes road distances, traffic, weather, and speed limits.
- Finding the absolute best route may be computationally expensive.
- You use a heuristic algorithm to find a reasonably good route quickly.
**Result:** The solution might not be optimal according to the model, but it could perform better in the real world.
**Approach 1 Vs Approach 2**
1. Approximate model + precise solution: Find the optimal solution to a simplified representation of reality. The result may be inaccurate in practice.
2. Precise model + approximate solution: Use a realistic representation of the problem but accept a near-optimal solution.
**Why is approach 2 preferred?**
A good solution to a realistic problem is often more useful than a perfect solution to an unrealistic problem.
**Key takeaway:** An algorithm can be mathematically optimal without being practically optimal.
## Constraints
**Hard constriants**
- Must be satified
- Determine feasibility
**Soft constraints**
- Preferably satisfied
- Determine solution quality
**Key takeaways**:
1. **Feasible solution**: Satisfies all hard constraints.
2. **Optimization**: Improve a feasible solution according to quantified soft constraints.
3. Constraints reduce the feasible search space but can make finding valid improvements more difficult.
## Dynamic Problems
- Real-world problems may change before, during, or after finding a solution.
- A previously optimal solution may become suboptimal when conditions change.
## General Problem-Solving Strategy
1. Identify the goal and available information
2. Construct a model of the problem
3. Define candidate solutions and constraints
4. Evaluate and compare solutions
5. Search for a feasible, high-quality solution
## Remember
- Search Space: All possible candidate solutions.
- Evaluation Function: Measures candidate quality.
- Model: Simplified representation of the problem.
- Feasible Solution: Satisfies all hard constraints.
- Optimal Solution: Best feasible solution under the chosen objective.
