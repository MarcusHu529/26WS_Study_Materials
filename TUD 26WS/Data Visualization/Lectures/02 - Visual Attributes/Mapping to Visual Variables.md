
## Visual Mapping and Semiology of Graphics
### Design goals
- *Expressiveness:* Visualization shows exactly the information in the data, nothing more.
- *Effectiveness:* Viewers can perceive and comprehend information quickly.
- Expressiveness is a mandatory requirement; effectiveness is optimized among valid representations.
- Example: Arbitrary bar lengths for nominal categories introduce an unintended order → not expressive.
### Bertin's graphical system
Three types of marks:
- *Point:* Represents a location.
- *Line:* Represents a boundary, route, or connection.
- *Area:* Represents a region with measurable size.
Eight visual variables:
- *Positional:* X, Y.
- *Retinal*: Size, shape, value (lightness), orientation, color (hue), texture.
*Monosemic encoding:* Each symbol has one meaning.
*Polysemic encoding:* Meaning depends on context.
### Perceptual properties

| Property     | Meaning                                                                       |
| ------------ | ----------------------------------------------------------------------------- |
| Associative  | Different symbols can be perceived as belonging to the same overall structure |
| Selective    | Particular groups can be visually distinguished                               |
| Ordered      | Values can be perceived in a ranking                                          |
| Quantitative | Numerical differences or ratios can be perceived                              |
| Length       | Number of distinguishable variations                                          |
- *Position:* Associative, selective, ordered, quantitative.
- *Size:* Selective, ordered, partially quantitative.
- *Value*: Selective and ordered.
- *Color (hue):* Associative and selective, not ordered.
- *Texture:* Associative and selective; can express multiple types of ordering.
- *Orientation:* Associative and selective.
- *Shape:* Mostly associative, not selective according to Bertin's table.
### Mapping guidelines
- *Position:* Best for all mapping goals; prefer it when available.
- *Color:* Associative and selective mapping.
- *Shape:* Purely associative mapping.
- *Size*: Selective, ordered, partially quantitative mapping.
## Maximizing Effectiveness
### Mackinlay's selection criteria
- *Expressivity*: Exactly the information in the data, nothing extra. Must be satisfied.
- *Effectiveness*: How quickly viewers grasp the information.
Mackinlay (1986) developed rankings for choosing visual encodings based on data type.
### Effectiveness rankings

| Rank | Quantitative | Ordinal          | Nominal     |
| ---- | ------------ | ---------------- | ----------- |
| 1    | Position     | Position         | Position    |
| 2    | Length       | Density          | Color hue   |
| 3    | Angle        | Color saturation | Texture     |
| 4    | Slope        | Color hue        | Connection  |
| 5    | Area         | Texture          | Containment |
*Main lesson*: The best visual encoding depends on the data type.
## Mapping to Color
### Applications and limitations
Color can be used for:
- Distinction and grouping.
- Emphasis and marking.
- Representation of natural phenomena (red = hot, blue = cold).
- Expression of mood.
### Color perception
Small objects make colors difficult to distinguish (small size color blindness).
Lecturer's approximate thresholds:
- Color discrimination: 16 arcminutes.
- Color identification: 20 arcminutes.
Viewing distance affects angular size.

| Term       | Definition                                                         |
| ---------- | ------------------------------------------------------------------ |
| Radiance   | Physical light power transported along rays                        |
| Luminance  | Radiance weighted by human spectral sensitivity; measured in cd/m² |
| Brightness | Absolute visual perception of luminance                            |
| Lightness  | Relative brightness in a scene; also called value or tone          |
*Important:* Equal luminance does not guarantee equal perceived brightness (White's illusion and Hermann grid).
### Nominal color palette design
*Lecturer's guidelines*:
- Prefer 5–8 colors for fast comprehension.
- Maximize perceptual distance.
- Prefer easily recognizable color names.
- Support associative and selective perception.
- For nominal data, use equal lightness to avoid introducing an unintended order.
*Linear separation*: For three colors, preattentive selective perception is supported when the third color is not on the line connecting the other two in perceptual color space.
*Lu′v′ color space*:
- Distances aim to represent perceived color differences.
- Fixing L produces an equal-lightness palette in the model.
- Select colors from different recognizable color categories.
*Equal brightness calibration*:
- Real displays may not reproduce theoretically equal-lightness colors equally.
- Kindlmann et al. use human face perception to adjust colors to a perceptual balance point.
### Quantitative and ordinal color scales
- *Transfer function*: Maps quantitative attributes to colors; usually adjustable.
- *Color scale*: A predefined mapping.
- *Unipolar*: One-directional range, such as nonnegative values.
- *Bipolar*: Negative and positive values around a meaningful center.
- *Ordinal palette*: Can be obtained by sampling a color scale at equal intervals.
*Design criteria*:
1. Preserve perceived order and quantitative differences.
2. Use obvious start and end colors; avoid cyclic scales.
3. Reserve strong hue changes for critical transitions, such as sign changes.
4. Make scales colorblind-safe.
*Why rainbow scales are problematic*: Their lightness is non-monotonic, so numerical order is not preserved perceptually.
*Evaluation:* The WhichBlair experiment used human face images and found a strong correlation between lightness contrast and the perceived appropriateness of color scales. 
### Bivariate color scales
A bivariate color scale represents two attributes using color.
Example from the lecturer:
- Winning party → Hue.
- Winning margin → Saturation.
Direct use of two RGB components is not perceptually adapted.
For two nominal variables, the lecturer recommends no more than approximately 4 × 4 or 3 × 5 colors to support preattentive perception.
## Mapping to Texture
### Texture and data types

| Data type    | Suitable texture properties                            |
| ------------ | ------------------------------------------------------ |
| Nominal      | Different texture/material categories                  |
| Ordinal      | Grain size, contrast, density, regularity, orientation |
| Quantitative | Grain size, contrast, orientation (cyclic values)      |
Texture is generally less effective than position or length for precise quantitative comparisons.
### Perceptual limitations
- Maximum contrast sensitivity: 1–10 cycles per degree.
- Approximate orientation resolution: 5°.
- Better orientation sensitivity near 0° and 90°.
- Approximately 24 distinguishable spatial frequencies in the studied 1–10 cycles/degree range.
A cycle is one complete repetition of a pattern.
### Gabor functions
A Gabor function combines a cosine oscillation with a *Gaussian window*.
$$G(\mathbf{x})= A\cos\left(2\pi f\,\mathbf{v}\cdot(\mathbf{x}-\mathbf{x}_0)\right) \exp\left(-\frac{\|\mathbf{x}-\mathbf{x}_0\|^2}{2\sigma^2}\right)$$

| Parameter      | Meaning                       |
| -------------- | ----------------------------- |
| $\mathbf{x}_0$ | Center position               |
| $A$            | Amplitude / contrast strength |
| $f$            | Frequency of oscillation      |
| $\mathbf{v}$   | Vibration direction           |
| $\sigma$       | Gaussian window size          |
- Increasing $f$ → More closely spaced stripes.
- Increasing $\sigma$ → Larger spatial extent.
- Increasing $A$→ Stronger oscillation amplitude.
- Visible stripe orientation is perpendicular to vibration direction.
Gabor functions are used to model low-level detectors of human vision.
### Texton mapping
A *texton* is a basic texture element, described by:
$$t_{\mathbf{x}_0,C,S,\varphi}$$

| Parameter | Meaning                                            |
| --------- | -------------------------------------------------- |
| $C$       | Contrast → Amplitude                               |
| $S$       | Size → Wavelength and Gaussian window size         |
| $\varphi$ | Orientation → Perpendicular to vibration direction |
Lecturer's mapping guidelines:
- Contrast: Stevens' Power Law, intensity range 0.1–1.0.
- Size: Stevens' Power Law for area, approximately 2–16 cycles per degree.
- Consider context effects on perceived size.
- Orientation: Linear mapping to a chosen interval.
Orientation intervals specified by the lecturer:
$$\begin{aligned} \text{Cyclic: } & [0,2\pi]\\ \text{Positive: } & [0,\pi/2]\\ \text{Signed: } & [-\pi/4,\pi/4] \end{aligned}$$
*Stevens' Power Law*: Physical stimulus changes are not necessarily perceived proportionally.
### Texture synthesis and sampling
A complete texture is synthesized by sampling texton positions and combining the textons through *additive blending*.

| Algorithm             | Principle                                             | Limitation                                        |
| --------------------- | ----------------------------------------------------- | ------------------------------------------------- |
| Uniform sampling      | Sample positions uniformly                            | Not contrast-preserving for changing texton sizes |
| Inverse area sampling | Acceptance probability inversely proportional to area | Irregular spatial distribution                    |
| Poisson disk sampling | Reject candidates too close to existing samples       | More complex implementation                       |
### Uniform sampling
Generate `n` random positions:$$x,y\in[0,1]$$
No distance or area-dependent acceptance test.
### Inverse area sampling
$$p_{\text{accept}}= \frac{\text{min\_texton\_area}} {\text{texton\_area\_at}(p)}$$
- Generate a candidate position.
- Generate random $r\in[0,1]$.
- If $r>p_{\text{accept}}$, reject and retry.
- Otherwise, accept and add the position to `P`.
Larger textons are accepted less frequently. The algorithm still collects `n` accepted positions.
### Poisson disk sampling
```
while (Q.collides(p, poisson_scale * size_at(p)));
```
- If `Q.collides(...)` is `true`, reject and retry.
- If `false`, accept.
- `P.push_back(p)` stores the accepted position.
- `Q.insert(p)` registers it in the quadtree for future collision checks.