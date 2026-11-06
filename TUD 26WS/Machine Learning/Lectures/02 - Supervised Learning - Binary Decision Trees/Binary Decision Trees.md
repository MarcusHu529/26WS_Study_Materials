## What is a Binary Decision Tree (BDT)?
**Definition:** A BDT is a model that makes predictions by repeatedly testing binary features (0 or 1).
- Internal nodes: Test a feature.
- Branches: 0 = No, 1 = Yes.
- Leaf nodes: Produce the final prediction.
- Root: Starting point of the tree.
- Subtree: A smaller tree starting from a particular node.
**Prediction:** Start at the root, follow the branch matching the feature value, and continue until reaching a leaf.
## Tree Depth and Regularization
**Definition:** Tree depth is the number of edges in the longest root-to-leaf path.
- A tree with one leaf has depth 0.
- Deeper trees can represent more complicated decisions.
- Regularization penalizes complex trees to encourage simpler models.
The learning objective balances:$$\boxed{\text{Total cost}=\text{Training loss}+\lambda\cdot\text{Tree depth}}$$Here, $\lambda$ controls how strongly we penalize complexity.
## Three Decision Tree Problems

| Problem             | Goal                                                   |
| ------------------- | ------------------------------------------------------ |
| Supervised Learning | Minimize training loss + complexity penalty            |
| Separation          | Find the smallest-depth tree with zero training errors |
| Separability        | Determine if a zero-error tree exists within depth $m$ |
**Key difference:** Supervised learning allows errors if the total objective is lower. Separation and separability require perfect classification.
## X3C — Exact Cover by 3-Sets
**Definition:** X3C is an NP-complete problem used to prove that optimal BDT learning is computationally hard.
**Example:** Given a set of $3n$ elements and candidate subsets containing exactly $3$ elements each, determine whether we can select $n$ subsets such that:
1. Every element is covered.
2. No elements overlap between selected subsets.
*Exact cover = Complete coverage + No overlap.*
## Reduction from X3C to BDT
We convert an X3C problem into a BDT classification problem.
- Original elements → Training samples labeled 1.
- Candidate subsets → Binary features.
- Feature value 1 → The element belongs to that subset.
- Special sample → All features are 0, actual label 0.
- Maximum tree depth → $m=n$.
*Why add the special sample?*
- Without it, every training sample would be labeled 1. A single leaf predicting 1 would solve the classification problem trivially. The special sample prevents this.
## Forward Proof: Exact Cover → BDT
Assume an exact cover exists.
- Construct a chain-shaped BDT using the \(n\) selected subsets as feature tests.
- If a feature equals 1, predict 1. Otherwise, continue to the next test.
- Every positive sample belongs to a selected subset, so it eventually receives prediction 1.
- The special all-zero sample always follows the 0-branches and receives prediction 0.
- The tree has depth $n=m$, satisfying the depth limit.
*Conclusion:* An exact cover guarantees a valid BDT.
## Reverse Proof: BDT → Exact Cover
Assume a perfectly classifying BDT exists with depth at most $m$.
**Step 1 — Simplification**
- The special all-zero sample follows only 0-branches.
- Any 1-child subtree branching off that path can be replaced with a leaf predicting 1, because only positive training samples can enter it.
- This preserves correct classification and does not increase depth.
- **Result:** A chain-shaped tree with at most $n$ tested subsets.
**Step 2 — Covering**
- Every original sample must be predicted as 1.
- A sample can only receive prediction 1 if it belongs to at least one tested subset.
- Therefore, the tested subsets must cover every original element.
**Step 3 — No overlap**
- $3n$ original elements to cover.
- At most $n$ selected subsets.
- Exactly 3 elements per subset.
To cover all $3n$ distinct elements, we must use exactly $n$ subsets without overlap.
The essential mathematical argument is:$$\boxed{3n\le 3N\le 3n}$$
where $N$ is the number of tested subsets.
This forces $N=n$. Any overlap would leave an element uncovered.
**Conclusion:** A valid BDT guarantees an exact cover.
## Final NP-Hardness Conclusion
$$\boxed{\text{Exact Cover exists}\iff\text{Valid BDT exists}}$$
The transformation is possible in polynomial time.
Since X3C is NP-hard, BDT separability is also NP-hard. The lecture's further reductions establish hardness for the corresponding separation and supervised learning problems.
**Practical meaning:** Finding an optimal decision tree can be computationally difficult, motivating heuristic learning algorithms.
## Things to Remember
- BDT: Internal nodes test binary features; leaves predict labels.
- Regularization: Penalizes tree depth to discourage unnecessary complexity.
- Three problems: Supervised learning balances loss and depth; separation minimizes depth with zero errors; separability asks whether a perfect tree exists within a depth limit.
- Forward proof: Exact cover → Construct a chain → Perfect predictions → Valid depth.
- Reverse proof: Simplify tree → Prove covering → Use counting to prove no overlap.
