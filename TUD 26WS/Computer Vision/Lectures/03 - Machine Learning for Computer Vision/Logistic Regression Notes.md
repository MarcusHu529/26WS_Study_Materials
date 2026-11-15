## 1. The big picture

**Goal:** Learn a model that predicts a binary label $y\in\{0,1\}$ from an input image.

| Stage | What it does |
| --- | --- |
| Input image $x$ | Represents the input using pixel values |
| Linear model $r=f_\theta(x)$ | Produces a real-valued score |
| Sigmoid $\sigma(r)$ | Converts the score into a probability |
| Predicted label $\hat y$ | Chooses the more probable class |

During training, we learn the weights $\theta$. During inference, we use those weights to classify new images.

## 2. Essential mathematical notation

| Symbol | Meaning |
|--------|--------|
| $S$ | Set of training samples |
| $V$ | Set of pixel positions |
| $X=\mathbb R^V$ | Space of real-valued images |
| $x_s$ | Image of sample $s$ |
| $(x_s)_v$ | Pixel $v$ of image $s$ |
| $y_s$ | Actual label of image $s$ |
| $\theta$ | Vector of learned weights |
| $\theta_v$ | Weight associated with pixel $v$ |
| $\Theta_v$ | Random variable for weight $v$ |
| $f_\theta(x_s)$ | Model's score for image $s$ |
| $\hat y_s$ | Predicted label, distinct from the actual label $y_s$ |
| $\lambda$ | Regularization strength |
| $\phi(\theta)$ | Complete objective minimized during training |

**Remember:** $s$ indexes samples; $v$ indexes pixels. Uppercase random variables and lowercase realized values are different.

## 3. Linear model and sigmoid — Slides 4–6

**Linear function**

$$
\boxed{f_\theta(x_s)=\langle\theta,x_s\rangle
=\sum_{v\in V}\theta_v(x_s)_v}
$$

The model multiplies every input value by its corresponding weight and sums the results. This is the lecture's model without a separate bias term.

**Sigmoid**

$$
\boxed{p_1=\frac{1}{1+e^{-r}}},\qquad r=f_\theta(x_s)
$$

$$
p_0=1-p_1
$$

The sigmoid converts any real score into a probability between 0 and 1.

| Score $r$ | Probability of label 1 | Predicted class |
|--------|--------|--------|
| $r>0$ | $p_1>0.5$ | 1 |
| $r=0$ | $p_1=0.5$ | Tie |
| $r<0$ | $p_1<0.5$ | 0 |

A prediction is not necessarily the actual label.

## 4. Probabilistic model and Gaussian prior — Slides 5–8

The lecturer models the relationship among input images, labels, and weights using probability distributions.

$$
P(Y_s\mid X_s,\Theta)
$$

Describes the label distribution given an image and the model parameters.

$$
P(\Theta_v)
$$

Describes the prior distribution of a model weight.

The lecturer assumes independent weight priors with a common standard deviation $\sigma>0$:

$$
\boxed{\Theta_v\sim\mathcal N(0,\sigma^2)}
$$

A Gaussian prior centered at zero favors smaller weights in terms of prior density.

- Smaller $\sigma$: Narrower distribution, stronger preference for weights near zero.
- Larger $\sigma$: Wider distribution, allowing larger weights more readily.

**Parameter learning:** Find weights that maximize the posterior density given the training images and labels. This is maximum a posteriori (MAP) estimation.

**Notation:** $\sigma(r)$ denotes the sigmoid function, while the scalar $\sigma$ in the Gaussian prior denotes its standard deviation. Their meanings come from context.

The image probability factors can be omitted from parameter optimization because the observed images are fixed and their model probabilities do not depend on $\theta$.

## 5. Logistic loss and L2 regularization — Slides 9–10

### Logistic loss

$$
\boxed{L(r,b)=-br+\ln(1+e^r)}
$$

Here $r$ is the model score and $b\in\{0,1\}$ is the actual label.

**Intuition:** Assigning a high probability to the actual label produces a small loss. Assigning a low probability produces a large loss.

The loss is derived by taking the negative logarithm of the probability assigned to the observed label:

$$
L(r,b)=-\left[b\ln p_1+(1-b)\ln(1-p_1)\right].
$$

The binary label acts as a switch: use $-\ln p_1$ when $b=1$ and $-\ln(1-p_1)$ when $b=0$.

### L2 regularization

$$
\boxed{R(\theta)=\|\theta\|_2^2
=\sum_{v\in V}\theta_v^2}
$$

The Gaussian prior leads to the regularization coefficient:

$$
\boxed{\lambda=\frac{1}{2\sigma^2}}
$$

Taking the negative logarithm of the independent Gaussian priors produces $\frac{1}{2\sigma^2}\sum_v\theta_v^2$ plus terms independent of $\theta$. Those constant terms do not affect the minimizing weights.

Regularization discourages large weights and can help reduce overfitting. Large weights do not automatically imply overfitting, and regularization does not guarantee better prediction accuracy.

### Complete optimization objective

This is the **most important equation in the lecture**:

$$
\boxed{
\phi(\theta)
=
\underbrace{\sum_{s\in S}L(f_\theta(x_s),y_s)}_{\text{Training loss}}
+
\underbrace{\lambda R(\theta)}_{\text{Regularization}}
}
$$

We want:

$$
\min_{\theta\in\mathbb R^V}\phi(\theta)
$$

The two components serve different purposes:

| Component | Purpose |
|--------|--------|
| Training loss | Fit the labeled training data |
| Regularization | Discourage excessively large weights |

Increasing $\lambda$ makes regularization more influential. The lecture uses a **sum** of sample losses; the relation $\lambda=1/(2\sigma^2)$ refers to that convention. Switching to a mean loss requires rescaling the regularization coefficient to preserve the same optimization problem.

## 6. Worked example — Complete objective calculation

Given:

$$
\theta=\begin{pmatrix}1\\-1\end{pmatrix},
\qquad \lambda=0.25
$$

Two training samples:

$$
x_1=\begin{pmatrix}2\\1\end{pmatrix},\quad y_1=1
$$

$$
x_2=\begin{pmatrix}1\\2\end{pmatrix},\quad y_2=0
$$

**Step 1 — Linear scores**

$$
r_1=1(2)-1(1)=1
$$

$$
r_2=1(1)-1(2)=-1
$$

**Step 2 — Logistic loss**

For sample 1:

$$
\begin{aligned}
L(1,1)&=-1+\ln(1+e^1)\\
&\approx0.3133
\end{aligned}
$$

For sample 2:

$$
\begin{aligned}
L(-1,0)&=\ln(1+e^{-1})\\
&\approx0.3133
\end{aligned}
$$

**Step 3 — Regularization**

$$
R(\theta)=1^2+(-1)^2=2
$$

$$
\lambda R(\theta)=0.25(2)=0.5
$$

**Step 4 — Total objective**

$$
\begin{aligned}
\phi(\theta)
&=L(1,1)+L(-1,0)+0.5\\
&\approx\boxed{1.1265}
\end{aligned}
$$

The result uses unrounded loss values before final rounding: $2(0.3132616875)+0.5\approx1.1265$. Adding the displayed rounded values $0.3133+0.3133+0.5$ gives $1.1266$, so keep extra digits until the final step.

**Interpretation:** This is the objective value for the current weights. It does not establish that those weights are globally optimal. Optimization would search for weights producing a lower objective.

## 7. Convexity and steepest descent — Slides 11–12

**Convexity:** Every local minimum of a convex function is a global minimum.

Lemma 2 states that the lecturer's L2-regularized linear logistic regression objective is convex. The proof is explicitly outside the scope of this course.

**Steepest descent:** Iteratively update the weights to decrease the objective.

$$
\boxed{\theta_{\text{new}}
=\theta-\eta\nabla\phi(\theta)}
$$

| Symbol | Meaning |
|--------|--------|
| $\nabla\phi(\theta)$ | Gradient, pointing toward steepest increase |
| $-\nabla\phi(\theta)$ | Direction of steepest decrease |
| $\eta$ | Step size |
| $\epsilon$ | Stopping tolerance |

The lecturer uses **line search** to choose a step size that minimizes the objective along the chosen direction:

$$
\eta=\arg\min_{\eta'\in\mathbb R}
\phi(\theta-\eta'\nabla\phi(\theta))
$$

The stopping tolerance $\epsilon$ sets how small the gradient norm should be before stopping. Exactly zero gradient certifies a global minimum for this differentiable convex objective on $\mathbb R^V$. A tolerance-based stop is an approximate numerical criterion, not a claim of exact optimality.

## 8. Inference — Slides 13–14

After training, the weights are fixed. We calculate the score for a new image and select the most probable label.

$$
\boxed{
\hat y_s=
\begin{cases}
1 & f_\theta(x_s)>0\\
0 & f_\theta(x_s)<0
\end{cases}
}
$$

When the score is zero, both labels are equally probable. A program needs a tie-breaking convention, such as predicting $1$ when the probability is at least $0.5$.

We do not need to calculate the sigmoid when we only want the binary classification, because the sigmoid crosses $0.5$ exactly at score zero.

**Important:** Finding a global minimum of the training objective does not guarantee that every unseen image will be classified correctly.

### Transfer example from the study

Suppose an unseen image has actual label $1$, but the trained model produces score $-3$:

$$
p_1=\frac{1}{1+e^3}\approx0.0474.
$$

The model predicts $0$, so this is a misclassification. It does not contradict convexity: convexity concerns minimizing the chosen training objective, while this error concerns performance on unseen data.

Increasing $\lambda$ changes the training objective and may change the learned weights, but it does not guarantee that this image will be classified correctly. In particular, merely multiplying all weights by a positive number $a<1$ reduces their magnitudes without changing the sign of the score:

$$
f_{a\theta}(x)=a f_\theta(x).
$$

For example, $a=0.5$ changes score $-3$ to $-1.5$, which still predicts $0$. Retraining with stronger regularization is not necessarily uniform scaling; this example shows why smaller weights alone do not guarantee a corrected prediction.

## 9. General supervised learning — Slides 15–17

The lecturer defines labeled data as:

$$
T=(S,X,x,y)
$$

Here $S$ indexes the samples, $X$ is the input space, $x$ assigns an input to each sample, and $y$ assigns its target label or output.

In this general framework, $\Theta$ denotes the **parameter space**, rather than the random weight vector used in the earlier probabilistic model.

The general supervised learning problem has the structure:

$$
\boxed{
\inf_{\theta\in\Theta}
\left[
\lambda R(\theta)+
\sum_{s\in S}L(f_\theta(x_s),y_s)
\right]
}
$$

Different supervised learning algorithms can use different model functions, losses, and regularizers.

**The lecture's L2-regularized logistic regression is a special case** of this general framework: it chooses a linear score, logistic loss, and squared L2 regularization.

---

## 10. Common mistakes and exam recognition cues

| If you see... | Think about... |
|--------|--------|
| $f_\theta(x_s)$ | Calculate the weighted linear score |
| $\sigma(r)$ | Convert score to probability |
| $L(r,b)$ | Compare score with actual label |
| $\lambda R(\theta)$ | Penalty for large weights |
| $\nabla\phi(\theta)$ | Gradient and optimization direction |
| $\arg\max$ / $\arg\min$ | Which input or parameters attain the best value |
| $\max$ / $\min$ | The best function value |
| $\inf$ | Greatest lower bound; it need not be attained |
| A negative score | Predict label 0 |
| A global training minimum | Does not guarantee correct unseen predictions |

The main mistakes to avoid are confusing predicted and actual labels, assuming large weights always cause incorrect predictions, and assuming that convexity guarantees generalization.

## 11. Final review — Three questions

Use these for future revision, without immediately looking up the answers.

1. **Conceptual:** Why do we combine logistic loss and L2 regularization instead of minimizing training loss alone?
2. **Mathematical:** Given weights, two labeled examples, and $\lambda$, can you calculate the complete objective?
3. **Application:** If a globally optimized model misclassifies an unseen image, what can you conclude—and what can you *not* conclude—about convexity, regularization, and generalization?

**Main takeaway:** Logistic regression learns weights that assign high probabilities to correct training labels while discouraging excessively large weights. Convexity helps us optimize the training objective, but generalization must still be evaluated on unseen data.

## Sources

- [Study Transcript conversation](chatgpt-conversation://6af1eb0d-fd10-83eb-a71b-66c8f259baa1): final study notes, worked examination example, and feedback on optimization versus generalization.
- [[01- Logistic Regression I.pdf|Computer Vision I — Logistic Regression lecture slides]].

---

## Neural Networks and Backpropagation — Study Continuation

TU Dresden · Computer Vision I · Machine Learning for Computer Vision (2/3), slides 3–13

Lecture material: [[02- Lgostic Regression II.pdf|Neural networks and backpropagation slides]]. This section continues the logistic regression study with neural-network score functions.

### 1. Artificial Neural Networks (Slides 3–5)

**Core idea:** In this lecture, an artificial neural network is a directed acyclic graph (DAG) of connected computations. Acyclic means that following directed edges never returns to the starting node.

Three types of nodes:

- **Input nodes ($V$):** Receive input values.
- **Hidden nodes ($V_h$):** Perform intermediate computations.
- **Output nodes ($V_o$):** Produce final results.

Important graph notation:

| Symbol | Meaning |
|--------|--------|
| $P_v$ | Parents: directly connected into $v$ |
| $C_v$ | Children: directly connected from $v$ |
| $A_v$ | Ancestors: all nodes that can reach $v$ |
| $D_v$ | Descendants: all nodes reachable from $v$ |
| $\theta$ | Model parameters, which control computations |

**Remember:** Parents are direct connections; ancestors can include indirect connections.

### 2. Forward Propagation (Slides 6–7)

**Purpose:** Calculate the network's output by passing values from input nodes through hidden nodes to output nodes.

The lecturer uses:

$$
\alpha_v^\theta(x)
$$

This is the **activation** (calculated value) of node $v$, for input $x$ and parameters $\theta$.

The final network output is:

$$
f_\theta(x)
$$

The local function $g_v^\theta$ calculates the activation at a non-input node from its parents' activations:

$$
\alpha_v^\theta(x)=g_v^\theta\!\left((\alpha_u^\theta(x))_{u\in P_v}\right).
$$

Input-node activations are the supplied input values. Process nodes in topological order, so every parent is evaluated before its child.

Forward propagation follows:

$$
\text{Input}\rightarrow\text{Hidden}\rightarrow\text{Output}
$$

### 3. Logistic Regression with Neural Networks (Slides 8–9)

The connection to previous studies:

| Linear logistic regression | Neural network logistic regression |
|--------|--------|
| Score $r=\theta^\top x$ | Score $r=f_\theta(x)$ |
| Linear score function | Can represent nonlinear functions when nonlinear node functions are used |
| Sigmoid converts score to probability | Same sigmoid |
| Logistic loss + regularization | Same objective structure |

The lecturer's objective function:

$$
\boxed{
\phi(\theta)=
\sum_{s\in S}
\left[
-y_sf_\theta(x_s)+\ln(1+e^{f_\theta(x_s)})
\right]
+\frac{1}{2\sigma^2}\|\theta\|_2^2
}
$$

Two purposes:

- **Logistic loss:** Penalizes assigning low probability to the actual training label.
- **Regularization:** Penalizes large parameters to help control overfitting.

Here $\sigma>0$ is the Gaussian prior's standard deviation from the earlier study, so $\lambda=1/(2\sigma^2)$. The scalar $\sigma$ is distinct from the sigmoid function $\sigma(r)$.

**Formula correction:** The conversation's final summary omitted the square in the denominator. The formula above follows the lecture slide and the earlier Gaussian-prior derivation.

Neural networks may produce **non-convex objectives**, so gradient descent is not generally guaranteed to find the global minimum.

### 4. Backward Propagation (Slides 10–12)

**Purpose:** Efficiently compute sensitivities through the network. The recurrence below first measures how node activations affect a chosen target node; the chain rule then connects these sensitivities to parameter derivatives.

Backpropagation uses the **chain rule**.

#### Lecturer's recurrence

Fix a target node $w$. For a node $u\ne w$ that can reach $w$:

$$
\boxed{
\Delta_{uw}=
\sum_{v\in C_u}
\frac{\partial g_v^\theta}{\partial\alpha_u^\theta}
\Delta_{vw}
}
$$

Understand the symbols:

- $\Delta_{uw}$: Sensitivity of target node $w$ to node $u$.
- $C_u$: Children of node $u$.
- $\frac{\partial g_v^\theta}{\partial\alpha_u^\theta}$: Local derivative of child $v$'s function with respect to the input received from $u$, evaluated at the current forward-pass activations.
- $\Delta_{vw}$: Child $v$'s sensitivity relative to target $w$.

**Most important rule:**

$$
\boxed{\text{Multiply along paths, add across paths.}}
$$

Special cases:

$$
\Delta_{ww}=1
$$

For $u\ne w$, if no directed path exists from $u$ to $w$:

$$
\Delta_{uw}=0
$$

Zero is a valid derivative: changes in $u$ cannot influence $w$. It does not mean backpropagation cannot calculate the result. Conversely, a path can exist and still have zero sensitivity at a particular input, for example because a local derivative is zero.

#### Processing order

Backpropagation uses a **reverse topological order**.

Children must be processed before their parents because a parent's sensitivity depends on its children's sensitivities.

A practical reading of the algorithm is:

1. Run forward propagation and retain the activations needed for local derivatives.
2. Set $\Delta_{ww}=1$; assign $0$ to nodes with no path to $w$.
3. Visit the remaining relevant nodes in reverse topological order.
4. At each node, multiply each local derivative by the child's already-computed sensitivity and add the contributions.

For two independent children $v_1,v_2$ between $u$ and $w$, both orders $w,v_1,v_2,u$ and $w,v_2,v_1,u$ work.

$$
\text{Output}\rightarrow\text{Hidden}\rightarrow\text{Input}
$$

### 5. Connected Worked Example

Use the following fixed-input example, with other parameters held fixed and $f_\theta(x)=\alpha_{v_4}$:

$$
\alpha_{v_3}=2+4\theta_0
$$

$$
\alpha_{v_4}=1+\alpha_{v_3}^2
$$

At $\theta_0=1$:

**Step 1 — Forward propagation**

$$
\alpha_{v_3}=2+4(1)=6
$$

$$
\alpha_{v_4}=1+6^2=37
$$

**Step 2 — Backward propagation**

Start from the output:

$$
\Delta_{v_4v_4}=1
$$

Calculate hidden-node sensitivity:

$$
\Delta_{v_3v_4}=2(6)\cdot1=12
$$

Find the direct effect of $\theta_0$ on $v_3$:

$$
\frac{\partial\alpha_{v_3}}{\partial\theta_0}=4
$$

Apply the chain rule:

$$
\boxed{
\frac{\partial f_\theta(x)}{\partial\theta_0}
=12\cdot4=48
}
$$

**Interpretation:** Near $\theta_0=1$, increasing $\theta_0$ by 0.01 increases the output by approximately 0.48.

This is the derivative of the **network output**, not yet the derivative of the training objective. Training also accounts for logistic loss and regularization.

Although the formula for $v_4$ does not explicitly mention $\theta_0$, it depends on $\theta_0$ through $\alpha_{v_3}$. Its direct local parameter derivative, holding parent activations fixed, is $0$; the derivative through the full computation is $48$.

For two branches with local derivatives $2$ and $4$ from $u$, and child-to-target sensitivities $3$ and $5$, the chain rule gives:

$$
\Delta_{uw}=2(3)+4(5)=26
$$

### 6. Complete Neural Network Training Process

| Stage | Action |
| --- | --- |
| 1. Forward propagation | Calculate network scores $f_\theta(x_s)$ |
| 2. Objective calculation | Combine logistic losses and regularization |
| 3. Backward propagation | Compute derivatives through the network and assemble the objective gradient |
| 4. Gradient descent | Use that gradient to update parameters |

Repeat until the stopping criterion is met.

Gradient descent updates parameters:

$$
\boxed{\theta\leftarrow\theta-\eta\nabla\phi(\theta)}
$$

Here, $\eta>0$ is the learning rate and $\nabla\phi(\theta)$ is the gradient of the complete objective. The second lecture uses a fixed step size; a step size that is too large can increase the objective.

### 7. Common Exam Mistakes

| Mistake | Correct understanding |
|--------|--------|
| Backpropagation updates parameters | Backpropagation computes derivatives; gradient descent updates parameters |
| $\Delta_{uw}=0$ means the algorithm cannot calculate it | Zero is the correct sensitivity when no path exists |
| Multiply all sensitivities together | Multiply along paths, add contributions across paths |
| Start backpropagation at input nodes | Process children before parents |
| An output derivative is the same as the loss gradient | The loss gradient also depends on the loss function and regularization |
| Neural networks always have non-convex objectives | Non-convexity is possible, not inevitable |

---

### 8. What to Remember for the Exam

Focus on these five abilities:

1. **Recognize** input, hidden, and output nodes and identify parents, children, and paths.
2. **Compute** activations through forward propagation.
3. **Explain** why a neural network can use the same logistic loss as linear logistic regression.
4. **Apply** the chain rule and the lecturer's backpropagation recurrence to calculate sensitivities.
5. **Explain** how forward propagation, loss, backpropagation, and gradient descent work together.

**Central takeaway:** A neural network is a composition of functions. Forward propagation evaluates those functions; backward propagation efficiently differentiates them; gradient descent uses the resulting objective gradient to learn better parameters.

### Sources for this section

- [Continue Neural Networks Study conversation](chatgpt-conversation://6af8cebc-17f4-83eb-aeb9-a9e092dd91f9): study notes, worked examples, and checkpoint corrections.
- [[02- Lgostic Regression II.pdf|Machine Learning for Computer Vision (2/3) lecture slides]].
