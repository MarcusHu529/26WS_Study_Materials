## Questions:
1. **Why use $c'$ in the cumulative distribution function?**
   - $c$ is the fixed intensity at which we evaluate $H(c)$. The dummy variable $c'$ runs through the intensities being counted. Thus $\sum_{c'\le c}h(c')$ counts all pixels with intensity at most $c$.
2. **Is histogram equilibration the same as $H$-equilibration?**
   - $H$-equilibration is the mapping $\xi_H(c)=c^-+(c^+-c^-)H(c)$ for a chosen $H$. Histogram self-equilibration uses the input image's own CDF as $H$. The formula has the same form; the difference is how $H$ is obtained.
3. **What is a fixed $H$-equilibrator?**
   - Choose $H$ once and keep it unchanged while processing images. Each output pixel then depends only on that pixel's input value, so this is a point operator. Recomputing $H$ from each whole image makes self-equilibration a global operator.
4. **Why does flipping a symmetric Gaussian kernel change nothing?**
   - With coordinates relative to the kernel center, $g(j,k)=g(-j,-k)$. Reversing both axes gives the same weights. For example, $[1,2,1]$ equals its reversal. Convolution and correlation therefore agree for this kernel when alignment and boundary handling match.
5. **Why use Euler's number in the Gaussian formula?**
   - $e^{-t^2/(2\sigma^2)}$ gives smooth, symmetric decay. The square makes opposite offsets receive equal weights; the negative exponent makes distant pixels contribute less. The natural exponential is convenient because its derivatives are simple. Another exponential base could describe the same family after changing the scale.
6. **Why divide each Gaussian weight by the sum of the weights?**
   - This division **is the normalization**, not an additional step after normalization. If $g(j)=w(j)/N$ with $N=\sum_{j=-m}^{m}w(j)$, then $\sum_jg(j)=1$. A constant-valued neighborhood keeps its brightness.
7. **Do the two 1-D Gaussian filters $g_0$ and $g_1$ represent width and height?**
   - They filter along the horizontal and vertical image axes. They are directional filters, not the dimensions themselves. Applying both implements a separable 2-D Gaussian blur.
8. **Do derivatives output all edges, like a skeleton of the objects?**
   - Derivatives measure brightness changes. Combining both components gives the gradient magnitude, which can resemble object outlines. Noise and texture also respond, while weak boundaries may be missed. An edge map is not a morphological skeleton. Smoothing, NMS, and thresholding help select useful edges.

## Basic Notation:
![[Pasted image 20261018111232.png]]
## What is an operator?
### Definition:
- An operator is a mathematical rule that takes something as input and produces something as output. In computer vision, the input is usually an image, and the output is another image.
	Phi of f is the processed image: $${\varphi(f)=\text{processed image}}$$
## Point Operator
### Definition:
- A point operator is an image processing function where the new value of each pixel depends only on that individual pixel (its current color and optionally its position $(x,y)$), without looking at any surrounding/neighboring pixels.
- What is NOT a Point Operator?
	- Operations that require neighboring pixels to compute the output are not point operators:
		- Blurring / Gaussian Smoothing: Averages a pixel with its adjacent neighbors.
		- Sharpening / Edge Detection (Sobel Filters): Calculates differences between neighboring pixel intensities.
### Breakdown:
- The Image Transformation: $\varphi$ :
	- $\varphi:C^{n_0 \times n_1} \to C^{n_0 \times n_1}$ taking an input image $f$ and transforming it into a new output image $\varphi(f)$ of the same dimensions.
- The Pixel Rule $(\chi)$:
	- $\chi: C \times n_0 \times n_1 \to C$ taking three pieces of information to compute a pixel's new color:
		- $f(x,y)$: The pixel's original color
		- $x$: The pixel's column index
		- $y$: The pixel's row index
- The Pointwise Formula:
$$\varphi(f)(x,y) = \chi(f(x,y), x, y)$$
### Examples:
- Position-Independent (Pure Color Transformations)
	- When the function $\xi: C \to C$ ignores the coordinates $(x, y)$ and transforms pixels purely based on their input color:
		- $$\varphi_\xi(f)(x, y) = \xi(f(x, y))$$
		- Invert / Negative: $\xi(c) = 255 - c$ (swaps light and dark).
		- Brightness Adjustment: $\xi(c) = \min(255,\max(0,c+b))$ (adds constant $b$ to every pixel and clips to the valid range).
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
- For $0<c<1$: $0<\gamma<1$ brightens, $\gamma>1$ darkens, and $\gamma=1$ leaves the image unchanged. The endpoints $0$ and $1$ stay fixed.
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
- Contrast Enhancement: Redistributes intensities using their cumulative distribution and can improve low contrast. For finite images, this does not guarantee a uniform histogram or use of the entire range $[c^-,c^+]$.
- Dynamic Range Use: Can make existing intensity differences easier to see, but cannot recover detail already lost to clipping.
- Feature Normalization: Can reduce some differences in intensity distributions before downstream tasks; it does not guarantee invariance to lighting.
### H-Equilibration
- For any color interval $C = [c^-, c^+] \subseteq \mathbb{R}$ and any nondecreasing function $H: C \to [0, 1]$ such that $H(c^+) = 1$, $H$-equilibration is the function:$$\xi_H: [c^-, c^+] \to [c^-, c^+], \quad \xi_H(c) = c^- + (c^+ - c^-) H(c)$$
	- $c^-$ and $c^+$: The minimum and maximum allowable color/intensity values (e.g., $c^- = 0$ and $c^+ = 255$ for standard 8-bit grayscale).
	- Proportional Mapping: Maps each pixel intensity $c$ based on its percentile rank $H(c)$ in the cumulative distribution.

### H-Equilibrator vs. Self-Equilibration
- Fixed $H$-Equilibrator ($\varphi_{\xi_H}$): When $H$ is a predetermined, fixed function independent of the image $f$, the mapping $\varphi_{\xi_H}: C^{n_0 \times n_1} \to C^{n_0 \times n_1}$ defined by $\varphi_{\xi_H}(f) = \xi_H \circ f$ is a point operator.
- Self-Equilibration: The transformed image $\varphi_{\xi_H}(f)$ obtained when the cumulative distribution function $H$ is calculated directly from the input image $f$ itself.
### Is Self-Equilibration a Point Operator?
- No, self-equilibration is a global operator. A point operator's output at coordinate $(x, y)$ depends exclusively on the single pixel value $f(x, y)$ (and its position). In self-equilibration, $H$ is derived from pixel intensities across the entire image. Changing one pixel can change the mapping and therefore affect output pixels elsewhere in the image.
### Numerical Example (Applying Self-Equilibration)
Continuing from your $2 \times 2$ image $f = \begin{pmatrix} 1 & 2 \\ 1 & 3 \end{pmatrix}$ with intensity range $[c^-, c^+] = [0, 3]$:
- Equilibration Formula:$$\xi_H(c) = c^- + (c^+ - c^-)  H(c) = 0 + (3 - 0) H(c) = 3 \cdot H(c)$$
- Pixel Intensity Remapping:
	- $c = 1 \implies \xi_H(1) = 3 \cdot H(1) = 3 \cdot 0.5 = 1.5$
	- $c = 2 \implies \xi_H(2) = 3 \cdot H(2) = 3 \cdot 0.75 = 2.25$
	- $c = 3 \implies \xi_H(3) = 3 \cdot H(3) = 3 \cdot 1.0 = 3.0$
- Equilibrated Image $\varphi_{\xi_H}(f)$:
	- $$\varphi_{\xi_H}(f) = \begin{pmatrix} 1.5 & 2.25 \\ 1.5 & 3.0 \end{pmatrix}$$
- This example becomes brighter overall; equalization does not always brighten an image. Its minimum output is $1.5$, so it also illustrates that the entire available range need not be used. Integer-valued output would require a rounding/quantization rule.
## Linear Operators
### What makes an operator linear?
- **Rules of linearity**
	- An operator $\varphi$ is linear if it satisfies both:
		- $\varphi(f+h)=\varphi(f)+\varphi(h)$
		- $\varphi(\alpha f)=\alpha\varphi(f)$
	- $f$ and $h$ are images; $\alpha$ is an ordinary scalar.
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
	- They match in this example. The general identity $2(\alpha f+\beta h)=\alpha(2f)+\beta(2h)$ establishes linearity for all inputs.
	- $\varphi(f)=f+10$
		- $\varphi(10+20)=40$
		- $\varphi(10)+\varphi(20)=20+30=50$
	- Results differ, so not linear
### General Linear Image Operator
- Equation:
	- $\varphi(f)(x,y)=\sum_{j=0}^{n_0-1}\sum_{k=0}^{n_1-1}a_{xyjk}f(j,k)$
	- $a_{xyjk}$ is the weight assigned to input pixel $(j,k)$ for output pixel $(x,y)$; $a_{xyjk}f(j,k)$ is its weighted contribution.
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
		- $g=\frac19\begin{bmatrix}1&1&1\\1&1&1\\1&1&1\end{bmatrix}$
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

|       Property       |          Formula           |             Plain Explanation              |
| :------------------: | :------------------------: | :----------------------------------------: |
|    Commutativity     |         $f*g=g*f$          | Swapping the inputs gives the same result  |
|    Associativity     |     $f*(g*h)=(f*g)*h$      |    Grouping does not affect the result     |
|    Distributivity    |     $f*(g+h)=f*g+f*h$      |   Convolution distributes over addition    |
| Scalar compatibility | $\alpha(f*g)=(\alpha f)*g$ | A constant multiplier can be moved outside |
- **0-Extension: $X(f)$**
	- An image has boundaries. What happens when the kernel reaches an edge?
		- 0-Extension imagines that everything outside the image has pixel value zero
	- $$ X(f)(x,y)= \begin{cases} f(x,y)&\text{if }(x,y)\in n_{0} \times n_{1}\\ 0&\text{outside the image} \end{cases} $$
- **Restriction: $R_{n_{0},n_{1}}$**
	- Restriction means taking a particular finite rectangular region from an infinite image.
	- Think of it as cropping.
- **Shift: $S_{jk}$**
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
*Weight normalization*
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
## Discrete Derivatives and Gradients

[[01-operators.pdf#page=19|Lecture slides 16–18, starting at PDF page 19]]

### Definition and intuition

A derivative measures how quickly something changes. In an image, it measures how quickly **brightness changes across space**. A strong response is an edge candidate, but can also come from noise or texture.

### Derivative filters

The lecture uses centered convolution kernels:

$$
d_0=\frac12\begin{bmatrix}1&0&-1\end{bmatrix},
\qquad
d_1=\frac12\begin{bmatrix}1\\0\\-1\end{bmatrix}.
$$

$$
\partial_0f=f*d_0,
\qquad
\partial_1f=f*d_1.
$$

Because convolution reverses the kernel, these give:

$$
\partial_0f(x,y)=\frac{f(x+1,y)-f(x-1,y)}{2},
$$

$$
\partial_1f(x,y)=\frac{f(x,y+1)-f(x,y-1)}{2}.
$$

- $\partial_0f$: brightness change along the horizontal axis; it responds strongly to a vertical intensity boundary.
- $\partial_1f$: brightness change along the vertical axis; it responds strongly to a horizontal intensity boundary.
- Positive response: brightness increases in the positive coordinate direction. Negative response: brightness decreases.
- A constant neighborhood gives zero response because the derivative kernel weights sum to zero.
- With cross-correlation instead of convolution, reverse these kernels to keep the same derivative sign.
- Boundary pixels need an extension rule, such as the zero extension defined earlier.

**Example:** If the left and right neighbors have intensities $10$ and $30$, the horizontal derivative at the center is $(30-10)/2=10$.

The derivative slide writes $g*d_i$ while defining derivatives of $f$; here $f*d_i$ is used consistently. If smoothing is performed first, apply the derivatives to that smoothed image.

### Gradient, magnitude, and direction

The gradient combines the two derivatives into a vector:

$$
\nabla f=
\begin{bmatrix}
\partial_0f\\
\partial_1f
\end{bmatrix}.
$$

Its magnitude measures the strength of local brightness change:

$$
\lVert\nabla f\rVert=\sqrt{(\partial_0f)^2+(\partial_1f)^2}.
$$

Its direction is:

$$
\alpha=\operatorname{atan2}(\partial_1f,\partial_0f).
$$

- $\nabla$ is pronounced “nabla.”
- The gradient points toward increasing brightness, approximately **perpendicular to the edge**, rather than along it.
- If both derivatives are zero, the gradient has no meaningful direction.
- For derivatives $3$ and $4$, the magnitude is $\sqrt{3^2+4^2}=5$.
- The derivative operators are linear. Taking their magnitude is nonlinear.

**Remember:** The gradient magnitude provides edge candidates. It does not by itself produce a clean, thin edge map or a morphological skeleton.

## Non-Linear Operators

An operator is nonlinear when it does not satisfy the linearity rules given earlier. Its output might depend on image-dependent weights, a median, a maximum, a threshold, or a connectivity decision.

### Bilateral Filter

[[01-operators.pdf#page=22|Lecture slides 19–20, starting at PDF page 22]]

#### Definition and intuition

A bilateral filter smooths an image while reducing averaging across strong intensity boundaries. A neighbor gets a large weight when it is both **spatially close** and **similar in intensity** to the center pixel.

Unlike Gaussian averaging, closeness in position alone is not enough. This helps preserve edges while smoothing within regions.

#### Notation

| Symbol | Meaning |
| --- | --- |
| $V=n_0\times n_1$ | Set of image pixels |
| $v\in V$ | Center pixel being processed |
| $v'\in N(v)$ | One pixel in the spatial neighborhood of $v$ |
| $d_s(v,v')$ | Spatial distance between two pixels |
| $d_c(f(v),f(v'))$ | Difference between their colors/intensities |
| $w_s$ | Nonnegative weight that decreases with spatial distance |
| $w_c$ | Nonnegative weight that decreases with intensity difference |
| $\nu(f)(v)$ | Sum of the combined weights at $v$ |

$N:V\to2^V$ assigns a subset of pixels to each center pixel; $2^V$ means the set of all subsets of $V$.

#### Formula

First calculate the normalization:

$$
\nu(f)(v)=\sum_{v'\in N(v)}
w_s\bigl(d_s(v,v')\bigr)
w_c\bigl(d_c(f(v),f(v'))\bigr).
$$

Then compute the weighted average:

$$
\beta(f)(v)=\frac{1}{\nu(f)(v)}
\sum_{v'\in N(v)}
w_s\bigl(d_s(v,v')\bigr)
w_c\bigl(d_c(f(v),f(v'))\bigr)f(v').
$$

The denominator must be positive. Including the center with positive weights at distance zero ensures this. Dividing by $\nu(f)(v)$ makes the effective weights sum to $1$.

#### Weight functions in the lecture

The lecture's example uses Euclidean spatial distance, absolute intensity difference, and:

$$
d_s(v,v')=\lVert v-v'\rVert_2,
\qquad d_c(a,b)=|a-b|,
$$

$$
w_s(t)=\frac{1}{\sigma_s\sqrt{2\pi}}
\exp\left(-\frac{t^2}{2\sigma_s^2}\right),
\qquad
w_c(t)=\frac{1}{1+t^2/\sigma_c^2},
$$

$$
N(v)=\{v'\in V:d_s(v,v')\le n\}.
$$

- $\sigma_s>0$ controls how far spatial influence extends.
- $\sigma_c>0$ controls tolerance for intensity differences. Smaller values discourage averaging across intensity boundaries more strongly.
- $n\ge0$ is the chosen neighborhood radius.
- The common constant factor in $w_s$ cancels between the numerator and normalization.

#### Example

Suppose the center intensity is $10$, and the neighborhood intensities are $[10,12,200]$. For illustration, suppose the combined spatial-and-color weights are $[1,0.8,0.01]$.

$$
\beta(f)(v)=\frac{1(10)+0.8(12)+0.01(200)}{1+0.8+0.01}
=\frac{21.6}{1.81}\approx11.93.
$$

The very different value $200$ contributes little. An unweighted average would be $74$, which would mix the two intensity regions strongly.

**Why nonlinear?** The color weights depend on the input image itself. Changing the image changes both the values being averaged and their weights.

### Median Operator

[[01-operators.pdf#page=24|Lecture slides 21–22, starting at PDF page 24]]

#### Definition

Replace each pixel by the median of its neighborhood's intensity samples:

$$
M(f)(v)=\operatorname{median}\{f(v'):v'\in N(v)\}.
$$

Each pixel contributes a sample, so repeated values count repeatedly. Sort the samples and select the middle one. An odd-sized neighborhood gives an unambiguous middle sample; even-sized neighborhoods need an explicit convention.

#### Example

$$
[10,11,12,13,255]
\quad\Longrightarrow\quad
\operatorname{median}=12.
$$

The mean is $60.2$, pulled upward by the outlier $255$. The median is less affected by isolated extreme values, making it useful for salt-and-pepper noise.

- It often preserves sharp transitions better than simple averaging, but can remove small details.
- It is nonlinear because selecting the middle ranked value is not a fixed weighted sum.
- Its result depends on the size and shape of $N(v)$.

### Morphological Operators

[[01-operators.pdf#page=26|Lecture slides 23–29, starting at PDF page 26]]

#### Binary images as sets

For a binary image $f:\mathbb Z^2\to\{0,1\}$, collect the foreground pixels into a set:

$$
A=f^{-1}(1)=\{v\in\mathbb Z^2:f(v)=1\}.
$$

Morphological operations change this foreground set using a small **structuring element** $B\subseteq\mathbb Z^2$. Its shape determines which structures are removed, expanded, or connected.

- $B+v=\{b+v:b\in B\}$ means place the structuring element at $v$.
- $-B=\{-b:b\in B\}$ is its reflection.
- A typical example is the centered $3\times3$ square $B=\{-1,0,1\}^2$.
- For finite images, the treatment of pixels outside the image must be specified; a common binary convention is background there.

An operation takes two sets $A,B$ and returns a set. Fixing $B$ gives an image operator: its output is $1$ exactly at pixels belonging to the resulting set.

#### Erosion: $A\ominus B$

$$
A\ominus B=\{v\in\mathbb Z^2:B+v\subseteq A\}.
$$

Keep the center $v$ only if **every pixel covered by the placed structuring element belongs to the foreground**.

- With a usual centered structuring element containing the origin, erosion shrinks foreground regions.
- It can remove small objects and break thin connections.
- It does more than remove isolated pixels: boundary pixels fail the fit test too.

**Example:** Eroding a filled $3\times3$ foreground square by a centered $3\times3$ square leaves only the center pixel.

#### Dilation: $A\oplus B$

$$
A\oplus B=\{v\in\mathbb Z^2:((-B)+v)\cap A\ne\varnothing\}.
$$

Equivalently:

$$
A\oplus B=\{a+b:a\in A,\ b\in B\}.
$$

Place a copy of $B$ at every foreground pixel and take their union. With a usual centered structuring element containing the origin, dilation expands foreground regions and can join nearby regions.

**Example:** Dilating one foreground pixel by a centered $3\times3$ square produces a $3\times3$ block.

**Reflection detail:** The overlap test uses $-B$. For symmetric elements such as a centered square or cross, $-B=B$.

#### Opening: erosion followed by dilation

$$
A\circ B=(A\ominus B)\oplus B.
$$

First remove places where $B$ does not fit; then expand the surviving regions again.

- Removes small foreground features and narrow protrusions that cannot accommodate $B$.
- Can break thin foreground connections.
- The dilation does not restore everything that erosion removed.

**Example:** For a large foreground block plus an isolated pixel, opening by a $3\times3$ square removes the isolated pixel while retaining the block's parts that can accommodate the square.

#### Closing: dilation followed by erosion

$$
A\bullet B=(A\oplus B)\ominus B.
$$

First expand the foreground; then erode it again.

- Can fill small background holes and close narrow gaps, depending on $B$.
- Can join nearby foreground regions.
- The erosion does not necessarily undo the connections created by dilation.

| Operation | Rule | Main effect with a usual centered element |
| --- | --- | --- |
| Erosion | All of $B$ must fit | Shrinks foreground |
| Dilation | Union of copies of $B$ | Expands foreground |
| Opening | Erosion, then dilation | Removes small foreground features |
| Closing | Dilation, then erosion | Fills small background gaps/holes |

**Useful properties:** Opening is anti-extensive, $A\circ B\subseteq A$, and closing is extensive, $A\subseteq A\bullet B$. Both are idempotent: repeating the same opening or closing does not change the result further, under the set-based definitions above.

### Connected Components Operator

[[01-operators.pdf#page=33|Lecture slides 30–32, starting at PDF page 33]]

#### Definition and connectivity

A connected component is a maximal group of selected pixels that can reach one another along paths of selected pixels. Give every pixel in one component the same label and different components different labels.

**Course convention:** This lecture labels connected **zero-valued** regions. Here the selected set is:

$$
Z=\{v\in V:f(v)=0\}.
$$

For $v,w\in Z$:

$$
\varphi(f)(v)=\varphi(f)(w)
\iff
\text{there is a path from }v\text{ to }w\text{ entirely inside }Z.
$$

The equivalence concerns the pixels being labeled. Pixels outside $Z$ are not members of those components. Other conventions label nonzero foreground instead; always check which set is selected.

- **4-neighborhood:** left, right, above, and below. This is the pixel grid graph defined in the lecture.
- **8-neighborhood:** also includes the four diagonal neighbors.
- Connectivity depends on the chosen neighborhood.
- A component can contain just one pixel; a path of length zero connects that pixel to itself.
- The particular label numbers are arbitrary; membership is what matters.

#### Example

$$
f=\begin{bmatrix}0&1\\1&0\end{bmatrix}.
$$

The two zero-valued pixels are two separate components under 4-connectivity. They form one component under 8-connectivity because they touch diagonally.

### Distance Operator

[[01-operators.pdf#page=36|Lecture slides 33–35, starting at PDF page 36]]

#### Definition

The lecture measures the shortest distance in the pixel grid graph from each pixel to a **nonzero** pixel:

$$
D(f)(v)=\min_{w\in V:\,f(w)\ne0}d_G(v,w).
$$

- $d_G(v,w)$ is the number of edges in a shortest graph path.
- A nonzero pixel has distance $0$ because it is already a target.
- On the full rectangular 4-neighbor grid, graph distance is Manhattan distance:

$$
d_G((x,y),(a,b))=|x-a|+|y-b|.
$$

- This differs from Euclidean distance $\sqrt{(x-a)^2+(y-b)^2}$. A diagonal displacement of one pixel in each axis has graph distance $2$, but Euclidean distance $\sqrt2$.
- If the image contains no nonzero pixel, there is no target; the minimum needs a separate convention, often infinity.

#### Example

For a single nonzero center pixel:

$$
f=\begin{bmatrix}0&0&0\\0&1&0\\0&0&0\end{bmatrix},
\qquad
D(f)=\begin{bmatrix}2&1&2\\1&0&1\\2&1&2\end{bmatrix}.
$$

**Remember:** Here, components group zero-valued regions, while distance measures how far pixels are from the nonzero set. Do not silently swap the target convention.

### Non-Maximum Suppression (NMS)

[[01-operators.pdf#page=39|Lecture slide 36, PDF page 39]]

#### Definition

Keep a response only when it is a maximum in its local neighborhood; suppress the others to zero:

$$
\varphi_{\mathrm{NMS}}(f)(v)=
\begin{cases}
f(v),&f(v)=\max\limits_{w\in N(v)}f(w),\\
0,&\text{otherwise}.
\end{cases}
$$

Take $N(v)$ to contain the center and the comparison neighbors.

#### Example

Comparing each value with its immediate left and right neighbors, using available neighbors at the ends:

$$
[1,5,2,7,1]
\xrightarrow{\mathrm{NMS}}
[0,5,0,7,0].
$$

Both $5$ and $7$ survive because they are **local** maxima. Keeping only the global maximum would discard other useful features elsewhere in the image.

- NMS selects locally strongest responses; it does not prove they are real edges or corners.
- The lecture uses equality with the maximum, so tied maxima can survive. A flat plateau is not necessarily reduced to one pixel.
- The neighborhood determines which responses compete with each other.

## Canny Edge Detection

[[01-operators.pdf#page=41|Lecture slide 38, PDF page 41]]

### Purpose and pipeline

Canny combines gradients, directional NMS, and thresholding to turn brightness changes into a thin edge map. Gaussian smoothing is commonly performed first to reduce noise.

The lecture's four stages are:

$$
\text{Gradient}
\longrightarrow\text{Directional NMS}
\longrightarrow\text{Double thresholding}
\longrightarrow\text{Weak-edge classification}.
$$

### 1. Compute gradient magnitude and direction

$$
g=\sqrt{(\partial_0f)^2+(\partial_1f)^2},
\qquad
\alpha=\operatorname{atan2}(\partial_1f,\partial_0f).
$$

$g$ measures edge strength; $\alpha$ tells us the direction across the edge. The printed magnitude on slide 38 omits the squares; the formula here agrees with the earlier gradient definition.

### 2. Directional non-maximum suppression

Compare each gradient magnitude with neighboring magnitudes in both directions **along the gradient direction**. Keep the local peak and suppress smaller responses beside it.

For example, for samples across an edge:

$$
[2,5,9,7,2]
\xrightarrow{\text{directional NMS}}
[0,0,9,0,0].
$$

Without this step, thresholding could keep several adjacent responses to the same boundary, producing a thick edge. In practice the direction can be quantized to nearby grid directions or used with interpolation.

### 3. Double thresholding

Let $q=\varphi_{\mathrm{directional\ NMS}}(g)$ be the magnitude image after NMS. Choose $0\le\theta_0\le\theta_1$, usually with positive thresholds.

| Condition | Classification |
| --- | --- |
| $q(v)\ge\theta_1$ | Strong edge candidate |
| $\theta_0\le q(v)<\theta_1$ | Weak edge candidate |
| $q(v)<\theta_0$ | Rejected candidate |

**Example from the conversation:** With $\theta_0=3$ and $\theta_1=8$:

| NMS magnitude | Classification |
| --- | --- |
| $2$ | Rejected |
| $5$ | Weak |
| $9$ | Strong |
| $7$ | Weak |

Why two thresholds? A real boundary can contain weaker segments. Keeping only values above the high threshold could break it into disconnected pieces.

### 4. Classify weak edges

**Lecture rule:** Retain every strong pixel. Retain a weak pixel only if an originally strong pixel lies in its immediate 8-neighborhood.

If $E_s$ and $E_w$ are the strong and weak candidate sets, the final edge set is:

$$
E=E_s\cup\{v\in E_w:N_8(v)\cap E_s\ne\varnothing\}.
$$

**Example:** Suppose three horizontally adjacent candidates after NMS have magnitudes $[8,5,4]$, with thresholds $4$ and $7$, and no other strong neighbor exists.

- $8$ survives as a strong pixel.
- $5$ survives because it is weak and directly adjacent to $8$.
- $4$ is discarded by the lecture rule: its immediate neighbor $5$ was weak, and $8$ is two steps away.

**Terminology distinction:** Standard Canny hysteresis can retain an entire connected chain of weak candidates linked to a strong candidate. It would also retain the $4$ in that example. The lecture states the simpler direct-neighbor rule. See the [OpenCV Canny explanation](https://docs.opencv.org/3.0-last-rst/doc/py_tutorials/py_imgproc/py_canny/py_canny.html).

### Key checkpoint from the conversation

| Step | Problem it addresses |
| --- | --- |
| Directional NMS | Several nearby responses make the same edge thick |
| Double thresholding and weak-edge classification | Real edges may contain weaker segments that need supporting evidence |

**Remember:** NMS determines which nearby positions are local peaks. Thresholding and weak-edge classification determine which of those candidates are retained.

## Structure Tensor

[[01-operators.pdf#page=43|Lecture slides 40–42, starting at PDF page 43]]

### Why one gradient is not enough

A single pixel's gradient tells us one direction and strength of brightness change. It does not tell us whether the surrounding patch is a straight edge or a corner.

Instead, inspect gradients across a neighborhood:

- **Flat region:** gradients are approximately zero.
- **Straight edge:** gradients mainly point along one common axis, perpendicular to the edge.
- **Corner-like region:** significant gradients occur in at least two independent directions.

Corners are useful for locating and matching image features because movement along either local direction changes the appearance of the patch.

### Definition

At a pixel $v$, assign nonnegative weights $k_v(w)$ to neighboring pixels $w\in N(v)$, with:

$$
\sum_{w\in N(v)}k_v(w)=1.
$$

The structure tensor is the $2\times2$ matrix:

$$
S(v)=\sum_{w\in N(v)}k_v(w)\,\nabla f(w)\nabla f(w)^T
$$

$$
=\sum_{w\in N(v)}k_v(w)
\begin{bmatrix}
(\partial_0f(w))^2&\partial_0f(w)\partial_1f(w)\\
\partial_0f(w)\partial_1f(w)&(\partial_1f(w))^2
\end{bmatrix}.
$$

| Symbol | Meaning |
| --- | --- |
| $v$ | Center pixel being examined |
| $N(v)$ | Its local neighborhood |
| $w$ | One neighboring pixel |
| $k_v(w)$ | Weight of that neighbor |
| $\nabla f(w)\nabla f(w)^T$ | Outer product of its gradient with itself |
| $S(v)$ | Weighted summary of local directional gradient energy |

In the lecture's notation, each row $A_{w\cdot}(v)$ is the gradient written as a row vector, so $A_{w\cdot}(v)^TA_{w\cdot}(v)$ is the same outer product.

The diagonal entries summarize squared changes along the coordinate axes. The off-diagonal entries record how the two gradient components occur together. Averaging outer products avoids cancellation of opposite gradients, such as $[2,0]^T$ and $[-2,0]^T$.

### Worked example: two different directions

Take two equally weighted gradients:

$$
\nabla f(w_1)=\begin{bmatrix}2\\0\end{bmatrix},
\qquad
\nabla f(w_2)=\begin{bmatrix}0\\2\end{bmatrix}.
$$

Then:

$$
S=\frac12\begin{bmatrix}4&0\\0&0\end{bmatrix}
+\frac12\begin{bmatrix}0&0\\0&4\end{bmatrix}
=\begin{bmatrix}2&0\\0&2\end{bmatrix}.
$$

Both independent directions have substantial variation, consistent with a corner-like patch.

If both gradients instead equal $[2,0]^T$, then:

$$
S=\begin{bmatrix}4&0\\0&0\end{bmatrix},
$$

which indicates variation concentrated in one direction: an edge-like patch.

### Directional interpretation

For a unit vector $r$, the weighted squared gradient projection in direction $r$ is:

$$
P_r(v)=\sum_{w\in N(v)}k_v(w)
\bigl(\nabla f(w)^Tr\bigr)^2
=r^TS(v)r.
$$

This asks: **How much brightness change does the neighborhood contain along a chosen direction?** Since it is a sum of nonnegative squared terms, $S(v)$ is positive semidefinite.

## Eigenvalues and Harris Corner Detection

[[01-operators.pdf#page=44|Lecture slides 41–43, starting at PDF page 44]]

### What the eigenvalues mean

The structure tensor is symmetric and has two nonnegative eigenvalues, ordered as in the lecture:

$$
\sigma_1\ge\sigma_2\ge0.
$$

They describe the maximum and minimum directional gradient energy:

$$
\sigma_1=\max_{\lVert r\rVert=1}r^TSr,
\qquad
\sigma_2=\min_{\lVert r\rVert=1}r^TSr.
$$

The corresponding orthonormal eigenvectors $s_1,s_2$ give the principal directions, which need not align with the horizontal and vertical image axes. With repeated eigenvalues, these directions are not unique.

The lecture's spectral decomposition is:

$$
S=\sigma_1s_1s_1^T+\sigma_2s_2s_2^T,
$$

so:

$$
P_r=\sigma_1(s_1^Tr)^2+\sigma_2(s_2^Tr)^2.
$$

### Flat region, edge, or corner?

| Eigenvalues | Interpretation |
| --- | --- |
| Both near zero | Little gradient energy: flat-like region |
| One large, one near zero | Change mainly along one axis: edge-like region |
| Both substantial | Change in two independent directions: corner-like region or texture |

In an ideal noiseless model, the first two cases use exact zeros. In real images, use “small” and “large” relative to the image scale, noise, and chosen thresholds.

When $\sigma_2=0<\sigma_1$, the contributing gradients lie along one common axis. Their magnitudes and signs can vary; they do not have to be identical.

**Why not just inspect the diagonal entries?** A diagonal edge can have nonzero horizontal and vertical derivatives while all gradients remain aligned. For example:

$$
S=\begin{bmatrix}1&1\\1&1\end{bmatrix}
\quad\Longrightarrow\quad
(\sigma_1,\sigma_2)=(2,0).
$$

Both diagonal entries are positive, yet this is edge-like. Eigenvalues account for the off-diagonal terms and reveal the independent directions.

### Examples from the conversation

For these diagonal matrices, the eigenvalues are the diagonal entries:

$$
\begin{bmatrix}0&0\\0&0\end{bmatrix}
\quad\text{flat},\qquad
\begin{bmatrix}8&0\\0&0\end{bmatrix}
\quad\text{edge-like},\qquad
\begin{bmatrix}8&0\\0&6\end{bmatrix}
\quad\text{corner-like}.
$$

The final checkpoint compared:

| Pixel | $\sigma_1$ | $\sigma_2$ | Interpretation |
| --- | --- | --- | --- |
| A | $0$ | $0$ | Flat-like |
| B | $12$ | $1$ | Mainly one direction; relatively edge-like |
| C | $9$ | $7$ | Strongest corner candidate of the three |

Pixel C is the strongest corner candidate because its **smaller eigenvalue is largest**. Pixel B's value $1$ is small relative to its first eigenvalue $12$; whether a response passes an absolute threshold depends on the scale.

### Why use the smaller eigenvalue?

A large $\sigma_1$ alone can indicate a strong edge. A large $\sigma_2$ guarantees that even the least-changing direction has substantial gradient energy, so variation is present in both independent directions.

This formalizes the checkpoint answer: **the smaller eigenvalue tells us whether the second direction also has substantial variation.**

### Harris detector as defined in this lecture

The lecturer defines the detector as:

$$
\boxed{\varphi_{\mathrm{NMS}}\circ\sigma_2}.
$$

Read composition from right to left:

1. Compute image gradients.
2. Form $S(v)$ in a neighborhood of every pixel.
3. Compute its smaller eigenvalue $\sigma_2(v)$, producing a response image.
4. Apply NMS to that response image to select locally strongest corner responses.

In practical feature selection, an additional response threshold can reject very small maxima. This threshold is separate from the lecture's displayed definition.

**Terminology distinction:** The lecture calls this a Harris detector. The conventional Harris response in other sources is:

$$
R=\det(S)-\kappa\bigl(\operatorname{tr}S\bigr)^2
=\sigma_1\sigma_2-\kappa(\sigma_1+\sigma_2)^2,
$$

with a chosen parameter $\kappa$. This is a different response formula; keep the lecture's smaller-eigenvalue definition when discussing its slides. See [OpenCV's Harris explanation](https://docs.opencv.org/4.12.0/dc/d0d/tutorial_py_features_harris.html).

## Final Review

| Topic | Essential takeaway |
| --- | --- |
| Point operator | Each output depends only on the same input pixel and possibly its coordinates |
| Gamma operator | Changes normalized intensities by $c^\gamma$ |
| Fixed $H$-equilibrator | Applies a fixed intensity mapping; a point operator |
| Self-equilibration | Builds $H$ from the whole image; a global operator |
| Linear filter | Computes a fixed weighted sum of input values |
| Convolution | Reverses the kernel relative to cross-correlation |
| Gaussian smoothing | Averages mainly nearby pixels; normalized weights preserve constant brightness away from boundary effects |
| Gradient | Measures the direction and strength of brightness change |
| Bilateral filter | Uses spatial and intensity similarity to reduce smoothing across edges |
| Median filter | Selects the neighborhood's middle intensity and resists isolated outliers |
| Erosion / dilation | Require a full fit / expand by copies of the structuring element |
| Opening / closing | Erode then dilate / dilate then erode |
| Connected components | Group selected pixels by paths; this lecture selects zero-valued pixels |
| Distance operator | Finds shortest graph distance to a nonzero pixel |
| NMS | Keeps local maxima; the lecture's equality rule allows ties |
| Canny | Combines gradients, directional NMS, and strong/weak edge decisions |
| Structure tensor | Summarizes neighborhood gradient energy across directions |
| Smaller eigenvalue | Measures the weakest directional variation; a large value supports a corner candidate |
| Harris, lecture definition | Applies NMS to the smaller-eigenvalue response image |

### Checkpoints to reinforce

1. **Erosion:** The entire structuring element must fit, not just the center pixel.
2. **Connectivity:** A component can contain one pixel; 4- and 8-connectivity can give different answers.
3. **Distance:** Four-neighbor graph distance is Manhattan distance on the full grid, not Euclidean distance.
4. **Canny:** NMS addresses edge thickness; double thresholding and classification address weak candidates.
5. **Corners:** Nonzero derivatives in both coordinate axes are insufficient. Look for substantial variation in two independent directions through the eigenvalues.

### Sources and notation

- Lecture definitions and topic order: [[01-operators.pdf]]. PDF page links above account for repeated slides; for example, numbered slide 43 is PDF page 46.
- Canny explanations, structure-tensor examples, and checkpoint answers: [Review Study Materials conversation](chatgpt-conversation://6aef5015-36a4-83ed-a23e-6bb1a1eba1f5).
- Supplementary terminology comparisons are linked next to the relevant Canny and Harris paragraphs.
- Inline formulas use dollar delimiters; displayed formulas use double-dollar delimiters, matching this Obsidian note.


