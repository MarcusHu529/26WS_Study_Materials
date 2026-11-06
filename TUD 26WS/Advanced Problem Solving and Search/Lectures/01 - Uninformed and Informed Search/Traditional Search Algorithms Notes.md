## Foundations of Search
### Search problems
#### A Search problem consists of:
- **Initial state**: Where the search begins.
- **Action**s: Available decisions from a state.
- **Transition**: The state reached after an action.
- **Goal test**: Determines whether a state is a solution.
- **Path cost**: Total cost of the actions taken.
The objective is to find a path from the initial state to a goal state, possibly with minimum cost.
#### Tree search vs. graph search
- Tree search can generate the same state multiple times.
- Graph search tracks visited/explored states to avoid unnecessary repetition.
- Avoiding repeated states matters especially when cycles exist.
### Important search terminology

| Term             | Meaning                                                                        |
| ---------------- | ------------------------------------------------------------------------------ |
| Complete         | Guaranteed to find a solution if one exists, under the algorithm's assumptions |
| Optimal          | Guaranteed to find a minimum-cost solution                                     |
| Time complexity  | Computational work required                                                    |
| Space complexity | Memory required                                                                |
| Frontier         | Nodes generated but not yet expanded                                           |
| Expanded node    | A node whose successors are generated                                          |
Common complexity notation:$$b=\text{branching factor}$$$$d=\text{depth of the shallowest goal}$$ 
$$ m=\text{maximum search depth} $$
Here, $b$ estimates the number of successors per node.
## Uninformed Search
Uninformed search algorithms do not use a heuristic estimate of the remaining distance to the goal.
### Breadth-First Search (BFS)
**Idea**: Explore nodes level by level, beginning with the shallowest.
- Data structure: FIFO queue.
- Complete under finite branching.
- Optimal when every step has the same positive cost.
- Time: $O(b^d)$.
- Space: $(O(b^d)$.
![[Pasted image 20261101174400.png|472]]
**Remember:** BFS finds the shallowest goal, which is not necessarily the cheapest goal when edge costs differ.
### Depth-First Search (DFS)
**Idea**: Follow one branch as deeply as possible, then backtrack.
- Data structure: Stack or recursion.
- Generally not optimal.
- Not complete on infinite-depth spaces without appropriate safeguards.
- Worst-case time: $O(b^m).$
- Tree-search space: $O(bm)$, using a depth-first frontier representation.
**Tradeoff**: Much lower memory use than BFS, but may waste time exploring deep, unpromising branches.
### Iterative Deepening DFS (IDDFS)
**Idea**: Repeat depth-limited DFS, increasing the depth limit:$$ 0\rightarrow1\rightarrow2\rightarrow3\rightarrow\cdots$$
- Combines BFS's shallowest-goal behavior with DFS's low memory requirements.
- Complete with finite branching.
- Optimal for equal step costs.
- Time: $O(b^d)$.
- Space: $O(bd)$.
**Why repeating work can still be efficient**: In an exponentially growing tree, most nodes are at the deepest level, so repeating the shallower levels adds comparatively little overhead.
### Uniform-Cost Search (UCS)
**Idea**: Always expand the frontier node with the lowest accumulated path cost.
$$\boxed{g(n)=\text{cost from the start to node }n}$$
- Uses a priority queue ordered by $g(n)$.
- Complete and optimal with suitable positive step-cost assumptions.
- Does not use a heuristic.
- Related to Dijkstra's shortest-path algorithm.
**Critical distinction:**
BFS minimizes the number of steps when step costs are equal.
UCS minimizes the total path cost, even when the number of steps differs.
#### Uninformed search comparison
| Algorithm | Expansion priority     | Optimality              |
| --------- | ---------------------- | ----------------------- |
| BFS       | Shallowest depth       | Equal step costs        |
| DFS       | Deepest current branch | No guarantee            |
| IDDFS     | Increasing depth limit | Equal step costs        |
| UCS       | Lowest \(g(n)\)        | Suitable positive costs |
## Informed Search
### Heuristics and evaluation functions
A heuristic estimates the remaining cost from a node to a goal.
$$\boxed{h(n)=\text{estimated remaining cost from }n}$$
The key symbols:

| Symbol   | Meaning                                      |
| -------- | -------------------------------------------- |
| $n$      | Current search node                          |
| $g(n)$   | Actual path cost from the start to $n$<br>   |
| $h(n)$   | Estimated remaining cost                     |
| $h^*(n)$ | True minimum remaining cost                  |
| f(n)     | Evaluation function used to prioritize nodes |
### Greedy Best-First Search
Greedy Best-First Search selects nodes using:
$$\boxed{f(n)=h(n)}$$
It chooses whichever node appears closest to the goal.
**Main weakness**: It ignores the cost already paid.
A node may look close to the goal but be part of a very expensive path.
Therefore, Greedy Best-First Search is generally **not optimal**.
### A* Search
A* combines the known cost and estimated remaining cost:
$$\boxed{f(n)=g(n)+h(n)}$$
The estimated total solution cost through node \(n\) equals the path cost so far plus the estimated remaining cost.
**Example:**
$$g(n)=7,\quad h(n)=4$$
$$f(n)=7+4=\boxed{11}$$
A* prioritizes the node with the smallest $f(n)$.
**Connection:**
- UCS: $f(n)=g(n)$.
- Greedy: $f(n)=h(n)$.
- A*: $f(n)=g(n)+h(n)$.
### Admissible heuristics
A heuristic is admissible if it never overestimates the true minimum remaining cost:
$$\boxed{h(n)\le h^*(n)}$$
Example:
- True minimum remaining cost: 10.
- Heuristic estimate: 7.
- Admissible at this node because $7\le10$.
**Intuition**: An admissible heuristic is optimistic.
Under the standard assumptions, admissibility guarantees optimality for A* tree search.
### Consistent heuristics
A heuristic is consistent if, for every transition from $n$ to $n'$:
$$\boxed{h(n)\le c(n,n')+h(n')}$$
where:
- $h(n)$: Estimated remaining cost at the current node.
- $c(n,n')$: Actual cost of moving to the successor.
- $h(n')$: Estimated remaining cost at the successor.
Consistency means the heuristic respects a triangle-inequality-like condition.
**Important relationship**:
- With $h(\text{goal})=0$, consistency implies admissibility.
- Admissibility does not necessarily imply consistency.
- With a consistent heuristic, f-values do not decrease along a path.
- Consistency supports optimal A* graph search without needing to reopen expanded nodes under the usual assumptions.
### Iterative Deepening A* (IDA*)
**Idea**: Combine A*'s evaluation function with iterative deepening.
Instead of a depth limit, use an f-cost threshold.
$$f(n)=g(n)+h(n)$$
- Start with an initial threshold, typically $h(\text{start})$.
- Perform depth-first search.
- Prune a branch when $f(n)$ exceeds the threshold.
- If no solution is found, increase the threshold to the smallest exceeded f-value.
- Repeat.
**Advantage**: Low memory consumption.
**Disadvantage**: Repeated searches can regenerate many nodes.
With appropriate assumptions and an admissible heuristic, IDA* can find an optimal solution.
### Recursive Best-First Search (RBFS)
**Idea**: Perform a best-first-style search recursively while limiting memory usage.
- Uses $f(n)=g(n)+h(n)$.
- Explores the best available successor while tracking an f-limit.
- Backtracks when the current branch becomes worse than the alternative limit.
- Remembers backed-up f-values so it can reconsider alternatives.
**Advantages**: Linear-depth memory use in the standard tree-search setting.
**Disadvantage**: Can regenerate previously explored nodes, increasing runtime.
### Informed search comparison
| Algorithm | Main idea                          | Main limitation            |
| --------- | ---------------------------------- | -------------------------- |
| Greedy    | Choose smallest h                  | Can return expensive paths |
| A*        | Choose smallest g+h                | High memory consumption    |
| IDA*      | DFS with increasing f-threshold    | Repeated expansions        |
| RBFS      | Recursive best-first with f-limits | Node regeneration          |
**Remember**: A* is not simply Greedy Best-First Search. The addition of $g(n)$ changes the objective from appearing close to minimizing estimated total cost.
## Dynamic Programming
### What is Dynamic Programming (DP)?
Dynamic programming solves a problem by combining solutions to smaller subproblems.
Two properties are central:
**Optimal substructure**: An optimal solution can be constructed using optimal solutions of smaller subproblems.
**Overlapping subproblems**: The same smaller subproblems occur repeatedly.
**The distinction**:

| Property                | Why it matters                                                            |
| ----------------------- | ------------------------------------------------------------------------- |
| Optimal substructure    | Justifies constructing an optimal solution from smaller optimal solutions |
| Overlapping subproblems | Makes caching calculations useful                                         |
**Remember**: DP is not merely caching. The recurrence must correctly express how subproblem solutions produce the desired solution.
### Bellman's Principle of Optimality
An optimal solution must contain optimal solutions to the relevant remaining subproblems.
In the stagecoach problem, an optimal route from the current state must continue optimally after the next decision.
Otherwise, replacing a suboptimal remaining route with a better one would improve the total route, contradicting optimality.
### Stagecoach problem
The goal is to find a minimum-cost path across several stages.
The recurrence we studied was:
$$\boxed{ f_n^*(s)=\min_{x_n} \left[c_{s,x_n}+f_{n+1}^*(x_n)\right] }$$

| Symbol           | Meaning                                                       |
| ---------------- | ------------------------------------------------------------- |
| $n$              | Current stage                                                 |
| $s$              | Current state                                                 |
| $x_n$            | Chosen next state                                             |
| $c_{s,x_n}$      | Immediate transition cost from $s$ to $x_n$                   |
| $f_{n+1}^*(x_n)$ | Optimal remaining cost from the next state                    |
| $\min_{x_n}$     | Choose the feasible next state giving the smallest total cost |
| $f_n^*(s)$       | Minimum cost achievable from state $s$ at stage $n$           |
The recurrence adds:
$$\boxed{\text{Immediate cost}+\text{Optimal future cost}}$$
### Optimal value vs. optimal decision
This distinction is important.
$$f_n^*(s)=\text{minimum achievable remaining cost}$$
$$x_n^*(s)=\text{optimal next-state decision}$$
**Optimal value:** How much does the best solution cost?
**Optimal decision:** Which action should we take to achieve it?
Storing only the optimal values does not automatically provide the full path unless we reconstruct the decisions.
### Memoization vs. Tabulation
|Top-down memoization|Bottom-up tabulation|
|---|---|
|Starts with the original problem|Starts with base cases|
|Recursively solves needed subproblems|Fills states in dependency order|
|Caches results|Stores results in a table|
|May avoid irrelevant states|Often calculates all table entries|
Both techniques reuse previous calculations.
**Critical distinction from our session:**
- Dependency explains why bottom-up DP calculates smaller subproblems first.
- Reuse explains why storing results reduces redundant work.
### Greedy vs. DP
**Greedy**: Selects a locally best decision without generally revisiting it.
**DP**: Evaluates decisions based on their total consequences, using optimal subproblem values.
A locally cheapest action does not necessarily lead to a globally optimal solution.
DP is useful when immediate cost and future cost must both be considered.
##  Matrix-Chain Multiplication
### Matrix multiplication fundamentals
For two compatible matrices:$$(m\times k)(k\times n)\rightarrow(m\times n)$$ The scalar multiplication cost is:$$\boxed{mkn}$$**Why?**
- The output contains $mn$ entries.
- Each entry requires $k$ scalar multiplications.
- Total: $mn\times k=mkn$.
**Important**: Parenthesization does not change the mathematical matrix product, but it can change the number of scalar multiplications.
### The example from our session
Our four matrices were:

| Matrix | Dimensions   |
| ------ | ------------ |
| $A_1$  | $20\times2$  |
| $A_2$  | $2\times15$  |
| $A_3$  | $15\times40$ |
| $A_4$  | $40\times4$  |
Dimension vector:$$\boxed{p=[20,2,15,40,4]}$$
Index mapping:$$p_0=20,\quad p_1=2,\quad p_2=15,\quad p_3=40,\quad p_4=4 $$
For matrix $A_i$, its dimensions are:$$\boxed{p_{i-1}\times p_i}$$
This is the source of the $i-1$ indexing in the recurrence.
### Matrix-chain DP recurrence
Define:
$$M(i,j)=\text{minimum scalar multiplication cost for }A_i\cdots A_j$$
Base case:
$$\boxed{M(i,i)=0}$$
A single matrix requires no multiplication.
Recurrence:$$\boxed{ M(i,j)= \min_{i\le k<j} \left[ M(i,k)+M(k+1,j)+p_{i-1}p_kp_j \right] }$$Breakdown:
- $i$: First matrix in the subchain.
- $j$: Last matrix in the subchain.
- $k$: Splitting point, after matrix $A_k$.
- $M(i,k)$: Minimum cost for the left subchain.
- $M(k+1,j)$: Minimum cost for the right subchain.
- $p_{i-1}p_kp_j$: Cost of joining the two resulting matrices.
The join cost comes from multiplying:$$(p_{i-1}\times p_k)(p_k\times p_j)$$
### Our completed DP table
|\(M(i,j)\)|\(j=1\)|\(j=2\)|\(j=3\)|\(j=4\)|
|---|---|---|---|---|
|\(i=1\)|0|600|2800|1680|
|\(i=2\)|—|0|1200|1520|
|\(i=3\)|—|—|0|2400|
|\(i=4\)|—|—|—|0|
Fill the table by increasing chain length:$$\boxed{1\rightarrow2\rightarrow3\rightarrow4}$$
For M(1,4), the three possible splits gave:

| Split | Total cost |
| ----- | ---------- |
| k=1   | 1680       |
| k=2   | 4200       |
| k=3   | 6000       |
Therefore:$$ \boxed{M(1,4)=1680} $$
### Reconstructing the optimal parenthesization
Use a splitting-point table $S(i,j)$ to store the best $k$ for each subproblem.
In our example:$$S(1,4)=1$$$$S(2,4)=3$$
$$S(2,3)=2$$
 Following the splitting decisions gives:$$\boxed{A_1((A_2A_3)A_4)}$$
 The optimal multiplication cost is:$$\boxed{1680}$$
 **Connection to stagecoach**: $M(i,j)$ stores the optimal value; $S(i,j)$ stores the optimal decision.
### Complexity of matrix-chain DP
Complexity of matrix-chain DP: $$\frac{n(n+1)}{2}=O(n^2)$$
Number of possible splitting points for $M(i,j)$:$$j-i$$
At most $O(n)$ per subproblem.
Therefore:$$\boxed{\text{Time}=O(n^3)}$$
$$\boxed{\text{Space}=O(n^2)}$$
**Very important distinction from our review:**
The $O(n^3)$ time complexity describes the work of the DP algorithm finding the optimal order, not the number of scalar multiplications required to perform the chosen matrix product.
The actual multiplication cost depends on the matrix dimensions.
For example, multiplying five $2\times2$ matrices requires 32 scalar multiplications, while five $100\times100$ matrices require 4,000,000 scalar multiplications using the standard multiplication algorithm.
## Connections Between All Algorithms

| Algorithm | What decision does it make?                    | Main idea                                     |
| --------- | ---------------------------------------------- | --------------------------------------------- |
| BFS       | Which shallowest node to expand                | Search by depth                               |
| DFS       | Which branch to explore next                   | Go deep, then backtrack                       |
| UCS       | Which node has smallest g                      | Minimize known path cost                      |
| Greedy    | Which node has smallest h                      | Follow estimated closeness                    |
| A*        | Which node has smallest g+h                    | Balance cost-so-far and estimated future cost |
| IDA*      | Which branch fits the current f-limit          | Combine heuristic search with low-memory DFS  |
| RBFS      | Which branch is best under its f-limit         | Recursive best-first search with backtracking |
| DP        | Which decision minimizes total subproblem cost | Solve and reuse subproblems                   |
### Search vs. Dynamic Programming
- Both can find optimal solutions, but their methods differ.
- Search algorithms explore possible states and paths toward a goal.
- Dynamic programming defines subproblems and recurrences, then combines stored subproblem solutions.
- They are connected: for example, a shortest-path problem can be approached using graph search or a DP recurrence depending on its structure.
## Complete vs. Partial Solutions
**Complete solution**: All decision variables are assigned. The candidate can be evaluated directly.
**Partial solution**: Either an incomplete assignment or a complete solution to a reduced problem.
- A partial assignment represents a subset of possible complete solutions.
- Partial solutions may require estimates or bounds for evaluation.
- Algorithms working with complete candidates can return a candidate if interrupted after one has been found.
- Algorithms working with partial solutions may not yet have a usable complete answer.
**Main challenge**: Organize the search subspaces efficiently and evaluate the quality of partial solutions.
## Exhaustive Search and Backtracking
For SAT with $n$ Boolean variables:$$\boxed{\text{Number of complete assignments}=2^n} $$
**Exhaustive search**: Enumerates all possible candidates when necessary to guarantee an answer.
**Backtracking**: Stops exploring a branch when its partial assignment cannot lead to a valid solution.
Example:$$ (x_1\lor x_2) $$
If $x_1=0$ and $x_2=0$, the clause is permanently false, so the entire branch can be pruned.
If $k$ of $n$ variables are already assigned:$$\boxed{\text{Assignments under the branch}=2^{n-k}} $$
**Key point**: Backtracking can eliminate many complete assignments without generating them individually.
## A* Optimality Proof
$$\boxed{f(G_2)=g(G_2)>g(G)\geq f(n)}$$
Where:
- $G$: An optimal goal.
- $G_2$: A suboptimal goal.
- $n$: A frontier node on an optimal path.
- $g(G)$: Optimal solution cost.
- $f(n)=g(n)+h(n)$: Estimated total cost through $n$.
**Explanation**:
1. $f(G_2)=g(G_2$), because $h(G_2)=0$.
2. $g(G_2)>g(G)$, because $G_2$ is suboptimal.
3. $g(G)\geq f(n)$, because the heuristic is admissible.
Therefore:$$\boxed{f(G_2)>f(n)}$$
A* prioritizes n over the suboptimal goal.
**Conclusion**: Under the standard A* assumptions, an admissible heuristic prevents tree-search A* from selecting a suboptimal goal before an optimal one.
## A* f-Contours
BFS explores by increasing depth; A* prioritizes nodes using $f(n)$.
Let:$$ C^*=\text{optimal solution cost} $$
The lecturer describes A* expansion as:

| Condition  | Behavior                                |
| ---------- | --------------------------------------- |
| $f(n)<C^*$ | Expanded                                |
| $f(n)=C^*$ | Some may be expanded                    |
| $f(n)>C^*$ | Not expanded before optimal termination |
**Key point**: Admissible heuristics help A* avoid exploring paths whose estimated lower bound exceeds the optimal solution cost.
**Precision**: The usual f-contour interpretation assumes the standard A* conditions; consistency guarantees nondecreasing f-values along paths.
## Proving Optimal Substructure
**Purpose**: Prove that an optimal solution can be constructed from optimal subproblem solutions.
Proof by contradiction:
1. Assume the complete solution $S^*$ is optimal.
2. Suppose it contains a suboptimal subsolution s.
3. Replace s with a better solution $s^*$.
4. If the replacement remains feasible and improves the objective, the original solution was not optimal — contradiction.
Important condition:$$\boxed{\text{Replacement must preserve feasibility}}$$
- **Shortest path**: Replacing a subpath with a shorter path supports the cut-and-paste argument.
- **Longest simple path**: A longer replacement subpath may repeat vertices, violating the simple-path constraint.
**Remember**: A better subsolution does not necessarily produce a better feasible complete solution.
## Recursion DAG and Overlapping Subproblems
**Fibonacci**: Repeated recursive calls calculate identical subproblems.
**Merge sort**: Recursive calls work on disjoint portions of the input.
- A recursion tree shows all recursive calls.
- A recursion DAG merges repeated instances of the same subproblem.
- Shared nodes in the recursion DAG indicate overlapping subproblems.
**Key distinction**:$$ \boxed{\text{Overlap}\Rightarrow\text{Memoization can avoid recomputation}} $$
Disjoint subproblems do not benefit from memoization merely because recursion is involved.
## Fibonacci — Space-Optimized DP
Recurrence:$$\boxed{F_n=F_{n-1}+F_{n-2}} $$To calculate \(F_n\), only the preceding two values are needed.

| Method                       | Time        | Auxiliary space |
| ---------------------------- | ----------- | --------------- |
| Naive recursion              | Exponential | O(n)            |
| Top-down memoization         | O(n)        | O(n)            |
| Bottom-up with full table    | O(n)        | O(n)            |
| Bottom-up with two variables | O(n)        | O(1)            |
Space optimization: Store only the previous values needed by future calculations.
Example:$$dp(i)=dp(i-1)+dp(i-3)$$A rolling window of the last three states is sufficient, giving O(1) auxiliary space.
## DP Implementation and Applicability
**Memoization vs. tabulation**:
- *Top-down*: Computes reachable subproblems as needed; useful for sparse state spaces.
- *Bottom-up*: Uses an explicit dependency order; useful for dense state spaces and space optimization.
**General DP runtime formula:**$$ \boxed{ T_{\mathrm{DP}} = \text{Number of distinct states} \times \text{Work per state} } $$Lecturer's DP applicability checklist:
1. Optimal substructure? Verify with cut-and-paste.
2. Overlapping subproblems? Check the recursion DAG.
3. Finite, manageable state space?
4. Well-defined base cases?
## Mistakes Worth Reviewing

| Common mistake                                           | Correct understanding                                                  |
| -------------------------------------------------------- | ---------------------------------------------------------------------- |
| BFS always gives the cheapest path                       | Only when path cost is proportional to depth, such as equal step costs |
| Greedy and A* are the same                               | Greedy uses h; A* uses g+h                                             |
| Admissibility and consistency are identical              | Consistency is a stronger condition under the usual assumptions        |
| DP works only because it caches results                  | Optimal substructure justifies the recurrence                          |
| Bottom-up ordering is just about avoiding repetition     | Dependency determines the calculation order                            |
| The cheapest immediate action must be globally optimal   | Future costs can change the best decision                              |
| $p_{i-1}$ means $p_i-1$                                  | The subscript is i-1; evaluate the index first                         |
| $O(n^2)$ is the exact number of matrix-chain subproblems | Exact count is $n(n+1)/2$                                              |
| $O(n^3)$ is the actual scalar multiplication cost        | It describes the DP algorithm's time complexity                        |
| $M(i,j)$ tells us the multiplication order               | It gives the optimal cost; split decisions must also be recovered      |
