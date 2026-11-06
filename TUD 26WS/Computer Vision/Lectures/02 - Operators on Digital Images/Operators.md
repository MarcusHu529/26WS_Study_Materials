## Questions:
1. Why using $c'$ in the Cumulative Distribution function?
2. Is Histogram Equilibration the same as H-Equilibration?
3. Explain Fixed $H$-Equilibrator
4. For symmetric kernels, such as a standard Gaussian blur, flipping changes nothing. What does this mean?
5. Why using Euler's number in Gaussian filter formula?
6. After normalizing the weights, what is the reason to divide each weight by the sum of all gaussian weigths in the range?
7. In the slides, there are two 1-D gaussian filters, g_0 and g_1, do they represent width and height?
8. Do derivative filters output all the edges(what i mean by this is that the pixel location where brightness change suddenly) in the image? if apply both vertical and horizontal, it can look like a skeletons of the things within the image.
## Basical Notation:
![[Pasted image 20261018111232.png]]
## What is an operatpr?
### Definition:
- An operator is a mathematical rule that takes something as input and produces something as output. In computer vision, the input is usually an image, and the output is another image.
	Phi of f is the processed image: $${\varphi(f)=\text{processed image}}$$
## Point Operator
### Definition:
- A point operator is an image processing function where the new value of each pixel depends only on that individual pixel (its current color and optional postion $(x,y)$, without looking at any surrounding/neighboring pixels.
- What is NOT a Point Operator?
	- Operations that require neighboring pixels to compute the output are not point operators:
		- Blurring / Gaussian Smoothing: Averages a pixel with its adjacent neighbors.
		- Sharpening / Edge Detection (Sobel Filters): Calculates differences between neighboring pixel intensities.
### Breakdown:
- The Image Transformation: $\varphi$ :
	- $\varphi:C^{n_0 \times n_1} \to C^{n_0 \times n_1}$ taking an unput image $f$ and transforming it into a new output image $\varphi(f)$ of the same dimentions.
- The Pixel Rule $(\chi)$:
	- $\chi: C \times n_0 \times n_1 \to C$ taking three pieces of information to compute a pixel's new color:
		- $f(x,y): The pixel's original color
		- $x$: The pixel's column index
		- $y$: The pixel's row index
- The Pointwise Formula:
$$\varphi(f)(x,y) = \chi(f(x,y), x, y)$$
### Examples:
- Position-Independent (Pure Color Transformations)
	- When the function $\xi: C \to C$ ignores the coordinates $(x, y)$ and transforms pixels purely based on their input color:
		- $$\varphi_\xi(f)(x, y) = \xi(f(x, y))$$
		- Invert / Negative: $\xi(c) = 255 - c$ (swaps light and dark).
		- Brightness Adjustment: $\xi(c) = \min(255, c + b)$ (adds constant $b$ to every pixel)
		- Thresholding / Binarization: $\xi(c) = 255$ if $c \ge 128$ else $0$.
- Position-Dependent
	- When the transformation uses the pixel's coordinate $(x, y)$ as well:
		- Vignetting Effect: Darkening pixels near the outer edges based on their distance $(x, y)$ from the image center.
		- Linear Gradient Overlay: Adding brightness proportional to the column coordinate $x$.

## Gamma Operator
### Definition: 
- The gamma operator (also called gamma correction) is a specific position-independent point operator that non-linearly adjusts the brightness and contrast of an image by raising each normalized pixel value to a power $\gamma$.
### Breakdown:
- Color Set ($C = [0, 1]$): Pixel intensities are represented as continuous values between $0$ (pure black) and $1$ (pure white)
- Pixel Mapping ($\xi$): The function $\xi: C \to C$ transforms a single pixel color $c$ via exponentiation:$$\xi(c) = c^\gamma \quad \text{for any } \gamma > 0$$
- Gamma Operator ($\varphi_\xi$): Applied to the whole image $f$, every pixel's intensity is replaced by $f(x, y)^\gamma$:
- $$\varphi_\xi(f)(x, y) = (f(x, y))^\gamma$$
![[Pasted image 20261017153140.png]]

## Histogram Equilibration
### Image Histogram $h(c)$: 
- The histogram $h: C \to \mathbb{N}_0$ is a function that counts how many pixels in the image that have the exact color/intensity value c:$$h(c) = \vert{}\{r \in n_0 \times n_1 \mid f(r) = c\}\vert{}$$
	- $r$: A pixel coordinate $(x, y)$ in the grid $n_0 \times n_1$.
	- $f(r) = c$: The condition that the pixel at coordinate $r$ has color value $c$.
	- $\vert{}\cdot\vert{}$: The set cardinality, giving the total count of pixels with color $c$.
### Cumulative Distribution of Colors $H(c)$
- The cumulative distribution function (CDF) $H: C \to [0, 1]$ measures the fraction of total pixels in the image that have an intensity less than or equal to $c$:$$H(c) = \frac{1}{n_0 n_1} \sum_{\substack{c' \in f(n_0 \times n_1) \\ c' \le c}} h(c')$$
	- $n_0 n_1$: The total number of pixels in the image (width $\times$ height).
	- $\sum_{c' \le c} h(c')$: Adds up the counts of all colors $c'$ that are less than or equal to $c$.
	- $[0, 1]$ range: Normalizing by $n_0 n_1$ ensures $H(c)$ ranges from $0$ (for intensities below the darkest pixel) up to $1.0$ (for the brightest pixel)
### Concrete Numerical Example
- Suppose a tiny $2 \times 2$ image ($n_0 n_1 = 4$ pixels) has intensity values ranging from $0$ to $3$:$$\text{Pixel grid } f = \begin{pmatrix} 1 & 2 \\ 1 & 3 \end{pmatrix}$$
	- Histogram counts $h(c)$
		- $h(0) = 0$ (no pixels equal 0)
		- $h(1) = 2$ (two pixels equal 1)
		- $h(2) = 1$ (one pixel equals 2)
		- $h(3) = 1$ (one pixel equals 3)
	- Cumulative distribution $H(c)$:
		- $H(0) = \frac{0}{4} = 0.0$
		- $H(1) = \frac{h(0)+h(1)}{4} = \frac{0 + 2}{4} = 0.5$ (50% of pixels $\le 1$)
		- $H(2) = \frac{h(0)+h(1)+h(2)}{4} = \frac{0 + 2 + 1}{4} = 0.75$ (75% of pixels $\le 2$)
		- $H(3) = \frac{h(0)+h(1)+h(2)+h(3)}{4} = \frac{4}{4} = 1.0$ (100% of pixels $\le 3$)
### Why Important?
- Contrast Enhancement: Automatically redistributes intensity values across low-contrast images to utilize the full available range $[c^-,c^+]$.
- Dynamic Range Optimization: Spreads out clustered pixel intensities, making hidden structural features and details visible in underexposed (dark) or overexposed (bright) region.
- Feature Normalization: Standarizes lighting variations across different images prior to downstream computer vision tasks like segmentation, object  detection, or classification.
### H-Equilibration
- For any color interval $C = [c^-, c^+] \subseteq \mathbb{R}$ and any monotonic function $H: C \to [0, 1]$ such that $H(c^+) = 1$, $H$-equilibration is the function:$$\xi_H: [c^-, c^+] \to [c^-, c^+], \quad \xi_H(c) = c^- + (c^+ - c^-) H(c)$$
	- $c^-$ and $c^+$: The minimum and maximum allowable color/intensity values (e.g., $c^- = 0$ and $c^+ = 255$ for standard 8-bit grayscale).
	- Proportional Mapping: Maps each pixel intensity $c$ based on its percentile rank $H(c)$ in the cumulative distribution.

### H-Equilibrator vs. Self-Equilibration
- Fixed $H$-Equilibrator ($\varphi_{\xi_H}$): When $H$ is a predetermined, fixed function independent of the image $f$, the mapping $\varphi_{\xi_H}: C^{n_0 \times n_1} \to C^{n_0 \times n_1}$ defined by $\varphi_{\xi_H}(f) = \xi_H \circ f$ is a point operator.
- Self-Equilibration: The transformed image $\varphi_{\xi_H}(f)$ obtained when the cumulative distribution function $H$ is calculated directly from the input image $f$ itself.
### Is Self-Equilibration a Point Operator?
- No, self-equilibration is a global operator. A point operator's output at coordinate $(x, y)$ depends exclusively on the single pixel value $f(x, y)$ (and its position). In self-equilibration, $H$ is derived by inspecting and counting pixel intensities across the entire $n_0 \times n_1$ grid of $f$. Modifying a single pixel anywhere in $f$ changes $H$, which alters the output value at every coordinate in the image.
### Numerical Example (Applying Self-Equilibration)
Continuing from your $2 \times 2$ image $f = \begin{pmatrix} 1 & 2 \\ 1 & 3 \end{pmatrix}$ with intensity range $[c^-, c^+] = [0, 3]$:
- Equilibration Formula:$$\xi_H(c) = c^- + (c^+ - c^-)  H(c) = 0 + (3 - 0) H(c) = 3 \cdot H(c)$$
- Pixel Intensity Remapping:
	- $c = 1 \implies \xi_H(1) = 3 \cdot H(1) = 3 \cdot 0.5 = 1.5$
	- $c = 2 \implies \xi_H(2) = 3 \cdot H(2) = 3 \cdot 0.75 = 2.25$
	- $c = 3 \implies \xi_H(3) = 3 \cdot H(3) = 3 \cdot 1.0 = 3.0$
- Equilibrated Image $\varphi_{\xi_H}(f)$:
	- $$\varphi_{\xi_H}(f) = \begin{pmatrix} 1.5 & 2.25 \\ 1.5 & 3.0 \end{pmatrix}$$
- The image become brighter overall.
## Linear Operators
### What makes an operator linear?
- **Rules of linearity**
	- An operator $\varphi$ is linear if it satisfies both:
		- $\varphi(f+h)=\varphi(f)+\varphi(h)$
		- $\varphi(\alpha f)=\alpha\varphi(f)$
	- $f$ and $h$ both are images, $\alpha$ is an orinary number
	- **Additivity**
		- Processing two images after adding them gives the same result as processing them separately and then adding the results.
	- **Homogeneity**
		- Multiplying all pixel values by 3 before processing is equivalent to processing first and multiplying the result by 3 afterward.
		Write them Together: $$\varphi(\alpha f+\beta h)=  
	\alpha\varphi(f)+\beta\varphi(h)$$
- Example:
	- $\varphi(f)=2f$
	- If $f = 10,h=20$, then:
		- $\varphi(f+h)=2(10+20)=60$
		- $\varphi(f)+\varphi(h)=20+40=60$
	- Both match, so linear
	- $\varphi(f)=f+10$
		- $\varphi(10+20)=40$
		- $\varphi(10)+\varphi(20)=20+30=50$
	- Results differ, so not linear
### General Linear Image Operator
- Equation:
	- $\varphi(f)(x,y)=\sum_{j=0}^{n_0-1}\sum_{k=0}^{n_1-1}a_{xyjk}f(j,k)$
	- $a_{xyjk}f(j,k)$ means how much should input pixel $(j,k)$ contribute to output pixel $(x,y)$?
	- Every output pixel is calculated as a weighted sum of input pixels.
- Example:
	- $f=\begin{bmatrix}10&20&30&40\end{bmatrix}$
	- With weights (0.1, 0.2, 0.3, 0.4):
	- $0.1(10)+0.2(20)+0.3(30)+0.4(40)=30$
	- A general linear image operator can use different weights for every input-output pixel pair.
### Kernels and Filtering
- The general operator above is expensive because every output pixel can depend on every input pixel. Usually, we only need nearby pixels.
- A kernel (also called a filter) is a small grid of weights that moves across the image.
	- Example:
		- $g=\frac19\begin{bmatrix}1&1&1&1&1&1&1&1&1\end{bmatrix}$
		- This is a 3 × 3 averaging filter.
		- Every output pixel becomes the average of nine nearby pixels.
- Formula: $$\varphi(f)(x,y)=\sum_{j=0}^{m_0-1}\sum_{k=0}^{m_1-1}g(j,k)f\left(x+j-\left\lfloor\frac{m_0-1}{2}\right\rfloor,y+k-\left\lfloor\frac{m_1-1}{2}\right\rfloor\right)$$
	- $\lfloor z\rfloor$: Round z down to the nearest integer
	- $x+j-\lfloor(m_0-1)/2\rfloor$: input pixel's horizontal position relative to the kernel center
	-  $y+k-\lfloor(m_1-1)/2\rfloor$: corresponding vertical position
	- Example:
		- For a 3 x 3 kernel: $$ \left\lfloor\frac{3-1}{2}\right\rfloor=1 $$
		- $f(x+j-1,y+k-1)$
		- When $j=0$, we look one position to the left of the center. When $j=1$, we look at the center. When $j=2$, we look one position to the right. The same idea applies vertically.
	- The formula simply describes sliding the kernel over the image, multiplying corresponding values, and adding them.
	- This operation is called cross-correlation in image processing.
	- It's written as $f\otimes g$ and later distinguishes it from mathematical convolution.
### Convolution
- **1-dimensional discrete convolution:**
	- $$\boxed{(f*g)(t)=\sum_{s=-\infty}^{\infty}f(t+s)g(-s)}$$
	- The important difference from the filtering formula is **kernel reversal**.
	- In 1D, convolution flips the kernel left-to-right before applying it.
- **2-dimensional discrete convolution:**
	- $$(f*g)(x,y)=\sum_{j=-\infty}^{\infty}\sum_{k=-\infty}^{\infty}f(x+j,y+k)g(-j,-k)$$
		- For each output pixel at (x,y), consider all input positions, multiply each input value by the corresponding reversed kernel value, and add the products.
		- Although the formula uses infinite sums, practical filters normally have finite support: almost all kernel weights are zero.

|       Property       |          Formula           |             Plain Explaination             |
| :------------------: | :------------------------: | :----------------------------------------: |
|    Commutativity     |         $f*g=g*f$          | Swapping the inputs gives the same result  |
|    Associativity     |     $f*(g*h)=(f*g)*h$      |    Grouping does not affect the result     |
|    Distributivity    |     $f*(g+h)=f*g+f*h$      |   Convolution distributes over addition    |
| Scalar compatibility | $\alpha(f*g)=(\alpha f)*g$ | A constant multiplier can be moved outside |
- **0-Extension: $X(f)$**
	- An image has boundaries. What happens when the kernel reaches an edge?
		- 0-Extension imagines that everything outside the image has pixel value zero
	- $$ X(f)(x,y)= \begin{cases} f(x,y)&\text{if}(x,y)\in n_{0} \times x_{1}\\ 0&\text{outside the image} \end{cases} $$
- **Restriction: $R_{n_{0},n_{1}}$**
	- Restriction means taking a particular finite rectangular region from an infinite image.
	- Think of it as cropping.
- **Shift: $S_{jk}$
	- $S_{jk}(f)(x,y)=f(x+j,y+k)$
	- This means the output pixel at $(x,y)$ takes its value from input coordinate $(x+j,y+k)$.
- **Reflection: $L$**
	- $L(f)(x,y)=f(-x,-y)$
	- This reverses both coordinate directions.
	- It is the mathematical version of the kernel flip used in convolution.
		- **$f\otimes g=f*L(g)$**
		- The earlier sliding-kernel operation equals convolution with a reflected kernel.
## Gaussian Averaging Filters
### Definition:
A Gaussian filter blurs images by calculating a weighted average of neighboring pixels in a way that it gives more weight to pixels near the center.
### Formula:
[[01-operators.pdf#page=15|01-operators, page 15]]
*Gaussian weight function*
$$w(t)=e^{-\frac{t^2}{2\sigma^2}}$$
	- $w(t)$: weight at distance $t$ from the center
	- $t$: distance along one image axis
	- $e$: Euler's number, 2.718
	- $\sigma$: sigma, controls how quickly weights decrease
	- $t^2$: distance squared
- With a small $\sigma$, weights fall rapidly with distance. With a large $\sigma$, more distant pixels receive substantial weights.
*Wight normalization*
$$N=\sum^m _{j=-m}w(j), g(j)=\frac{w(j)}{N}$$
- Normalization makes sure the weights sum to 1, preserving constant brightness during averaging away from boundary effects.
- Add all the Gaussian weights within the chosen finite filter range.
- In slides, there are two 1D filters
	- $g_0$ : Horizontal Gaussian filter
	- $g_1$ : Vertical Gaussian filter
	- $f*g_0*g_1$: Gaussian smoothing in both directions
- *Why separate those two direction?*
	- Two 1D filterings are computationally cheaper than a full 2D directly.
### Image Sharpening
$$2f-(f*g_{0}*g_{1})$$
$$f+(f-\text{blurred }f)$$
**Remember**: Gaussian blurring suppresses local variation; this sharpening operation enhances it.
### Discrete Derivatives and Gradients
*Recall:* A derivative measures how quickly something changes.
**Concept:** In an image, we look at how quickly *pixel brightness changes across space.* An edge is found if there is a sudden brightness change.
**Derivative filters:**

**Gradient:**
$$\nabla f= \begin{bmatrix} \partial_0 f\\ \partial_1 f \end{bmatrix} $$
	-$\nabla$ : Nabla, the gradient.
	- $\partial_0 f$ : Brightness change along the first axis
	- $\partial_1 f$ : Brightness change along the second axis
- Magnitude:
$$|\nabla f|=\sqrt{(\partial_0f)^2+(\partial_1f)^2}$$
- A large egradient magnitude means a strong local intensity change.
- *Remeber*: The gradient describes direction and strength of brightness change. Its magnitude is to identify potential edges.
## Non-Linear Operators
**Definition**: 
- a metric that express the width and height. A decreading function $w_{s}: \mathbb R^+_{0} \to [0,1]$
- a metric that express the color. A decreading function $w_{c}: \mathbb R^+_{0} \to [0,1]$
- a function N that maps to all subset of pixels in the images for every pixel V, a set $N(V)$ called spatial neighborhood of $V$
-  Normalization, for any digital image f in V, and pixel in V.
- Bilateral filter, reducting edges, smoothing images
$$β(f)(v)=\frac{1}{ν(f)(v)}\sum_{v'(f)(v)}w_{s}(d_{s}(v,v'))w_{c}(d_{c}(f(v),f(v')))f(v')$$
**Example**:


**Median Operator**

**Morphological Operator**
A morphological operation is a function that maps subsets of pixels X subsets of pixel to subset of pixels.
A morphological operator: for any (typically finite) B is a subset of z^2 called structuring elements, and any elemental operation


