# Data Visualization — Lecture 2: Perception & Cognition

Lecture notes from the study conversation, with the worked examples and application checkpoints. Slides: [[DataVis-2026-02_Perception.pdf]].

## 1. Visual Perception (Slides 4–14)

**Core idea:** Seeing is not simply recording an image. Our brain actively interprets visual information.

- **Bottom-up processing:** Visual features → patterns → object recognition.
- **Top-down processing:** Previous knowledge and experience influence interpretation.
- **Visual perception is relative:** The same color or brightness can appear different depending on its surroundings.

Example: The same gray square appears brighter against a black background than against a white background.

**Takeaway:** What we display is not always what people perceive.

## 2. The Visual System (Slides 15–31)

### Rods vs. Cones

| Rods | Cones |
|--------|--------|
| Sensitive to low light | Work best in brighter conditions |
| No color discrimination | Enable color vision |
| Lower spatial resolution | Support fine detail |
| Common in the periphery | Concentrated in the fovea |

### Pattern Recognition

The brain recognizes complex objects by combining simpler features.

**Edges → Shapes → Patterns → Objects**

The **“What” pathway** helps identify objects.

**Channel Theory:** Different visual channels, such as color, form, and motion, can help separate information. Interference can occur within channels.

Example: Represent temperature with color and wind with motion instead of using color for both.

## 3. Preattentive Processing (Slides 32–40)

**Core idea:** Certain visual differences can be detected almost immediately, without searching each object individually.

Important examples: color, orientation, size, and motion.

- **Pop-out:** One red circle among blue circles is immediately noticeable.
- **Conjunction Search:** Finding a red circle among red squares and blue circles requires combining features and is generally slower.
- **Redundant Coding:** Encode the same information through more than one visual property, for example a warning category using both color and shape. This reinforces the message and avoids relying on color alone.

**Takeaway:** Make important information visually distinctive, but avoid excessive visual complexity.

## 4. Gestalt Principles (Slides 41–53)

**Core idea:** Our brain automatically organizes individual visual elements into meaningful groups.

| Principle | Intuition |
|--------|--------|
| Figure / Ground | Separate objects from background |
| Similarity | Similar appearance → same group |
| Proximity | Nearby objects → same group |
| Connectedness | Visually connected objects → same group |
| Common Region | Objects inside the same visible enclosure → same group |
| Symmetry | Mirrored structures form organized patterns |
| Closure | Brain fills missing parts |
| Continuity | Brain prefers smooth, continuous paths |
| Simplicity (Prägnanz) | Brain prefers simple organized structures |
| Common Fate | Objects moving together appear related |

**Important relationships from the lecture:**

- In the lecture's competing-cue examples, proximity can dominate similarity.
- Connectedness can override both proximity and similarity.
- Treat these as useful tendencies illustrated by the examples, not an unconditional ranking for every layout.

Example: In a dashboard, grouping related charts together and placing a boundary around them makes their relationship easier to recognize.

**Takeaway:** Use Gestalt principles to communicate real relationships, not create misleading ones.

## 5. Color and Contrast (Slides 54–68)

**Color perception depends on context**, including background, illumination, saturation, and object size.

Key ideas:

- The luminance channel conveys fine details and motion more effectively than chromatic channels.
- Differences in brightness and saturation can attract attention, depending on the surrounding colors and contrast.
- Use more saturated colors for small symbols and thin lines; less saturated colors for large areas.
- Avoid using pure blue for small text and thin lines.

### Color Spaces

| Color space | Main purpose |
|--------|--------|
| RGB | Screen color representation |
| HSV | Intuitive adjustment of hue, saturation, and value |
| CIELUV (LUV) | Designed to make numerical color distances more closely match perceived differences than raw RGB distances |

**Takeaway:** Choose colors based on how people will perceive them, not merely their numerical RGB values.

## 6. Nonlinear Perception (Slides 69–72)

**Core idea:** Physical changes and perceived changes are not necessarily proportional.

### Detection Threshold vs. JND

- **Detection Threshold $\Phi_0$:** Smallest stimulus that can be perceived.
- **Just-Noticeable Difference $\Delta\Phi$:** Smallest noticeable change in an existing stimulus.

**Weber's Law:** Noticeable changes depend on the initial stimulus magnitude.

**Fechner's Law:** Perceived intensity grows approximately logarithmically over a limited stimulus range.

### Stevens's Power Law

$$
\boxed{\Psi=k\cdot\Phi^a}
$$

| Symbol | Meaning |
|--------|--------|
| $\Phi$ | Physical stimulus |
| $\Psi$ | Perceived magnitude |
| $k$ | Scaling constant |
| $a$ | Perception exponent |

- $0<a<1$: Perceived magnitude grows sublinearly as the physical stimulus increases.
- $a=1$: Perceived magnitude grows proportionally.
- $a>1$: Perceived magnitude grows superlinearly.

### Worked Example — Circle Size

Company A has €10M revenue, Company B has €40M.

Revenue ratio:

$$
\frac{40}{10}=4
$$

If we want B's physical circle area to be four times A's:

$$
A=\pi r^2
$$

$$
\frac{r_B}{r_A}=\sqrt{4}=\boxed{2}
$$

The lecture's exponent table associates $a=0.7$ with the visual area of a projected square; the following slide applies this approximate area-perception model to circles:

$$
\Psi_{\text{area}}\propto A^{0.7}
$$

Thus, four times the physical area gives only approximately $4^{0.7}=2.64$ times the perceived area under this model.

For the perceived magnitude to be proportional to the data value $f$ under this model:

$$
\Psi\propto A^{0.7}\propto(r^2)^{0.7}=r^{1.4}.
$$

Therefore, the lecture derives:

$$
\boxed{r\propto f^{5/7}}
$$

Here, $f$ represents the data value. A data ratio of $4$ would require a radius ratio of $4^{5/7}\approx2.69$ under this perceptual model, compared with $2$ for area-proportional circles.

| Intended relationship | Radius rule |
| --- | --- |
| Physical circle area proportional to the data | $r\propto\sqrt{f}$ |
| Perceived magnitude proportional to the data under the $a=0.7$ model | $r\propto f^{5/7}$ |

These rules serve different purposes. The exponent is a model used in the lecture, not an exact rule for every viewer or viewing condition. With perceptually adjusted scaling, physical area is no longer directly proportional to the data, so the encoding must be stated clearly.

**Takeaway:** Mathematically proportional visualizations are not necessarily perceptually proportional.

### Contrast Sensitivity

Spatial frequency describes how fine or coarse a pattern is: tightly packed light–dark alternations have higher spatial frequency. Our visual system's sensitivity to contrast depends on that frequency.

Very fine, tightly packed patterns can become difficult to distinguish, especially from a distance.

---

## 7. Exam Recognition Cues

| If the question involves… | Think about… |
|--------|--------|
| Something immediately standing out | Preattentive Processing |
| Why objects appear grouped | Gestalt Principles |
| Previous knowledge influencing recognition | Top-down Processing |
| Combining edges into recognizable objects | Pattern Recognition |
| Two colors looking different depending on context | Color Contrast |
| Actual versus perceived quantities | Stevens's Power Law |
| Minimum noticeable change | JND / Weber's Law |
| Fine patterns becoming difficult to see | Contrast Sensitivity |

## 8. Applying the Principles — Examples from the Study

### Department membership

The original display used department colors but scattered employees randomly. Your redesign combined compatible grouping cues:

| Design choice | Principle |
| --- | --- |
| Consistent color and shape within each department | Similarity |
| Place employees from the same department close together | Proximity |
| Add a visible boundary around each department | Common region |

These choices reinforce the same relationship, making department membership easier to identify. Use distinct styles across departments without adding more categories than viewers can readily distinguish.

### Urgent-patient dashboard: a visualization example

The example displayed 100 patients, including 8 marked as urgent, using small equal-sized red/green circles, random positions, and a dark background.

Your improvements were:

- Make urgent entries easier to notice with larger symbols and a distinct shape or warning icon.
- Use color together with another cue, so urgency does not depend only on distinguishing red from green.
- Group urgent entries and enclose them in a common region **when this does not disrupt another meaningful order**, such as location or time.
- Check symbol and text contrast under the expected viewing conditions.

A dark background is not inherently a problem. The important question is whether text, symbols, and categories remain distinguishable against it. Random positioning is unhelpful here because it does not communicate a useful relationship.

### Focused revision

The conversation identified quantitative perception and contrast sensitivity as the areas to revisit:

1. Explain the difference between detection threshold and just-noticeable difference.
2. For a data ratio of $4$, distinguish the area-proportional radius ratio $2$ from the model-based perceptual ratio of about $2.69$.
3. Explain why an accurately drawn fine pattern can still be hard to distinguish at a greater viewing distance.

## Final Mental Model

Data → visual encoding → visual perception → cognitive interpretation → understanding → decision.

The most important lesson from this lecture:

**A good visualization does not merely represent data accurately. It helps the human visual system interpret that data accurately and efficiently.**

## Sources

- [Study Visualization Intuition conversation](chatgpt-conversation://6afb9c5e-b944-83eb-be86-0356cecc270d): lecture summary, worked circle-size example, and application checkpoints.
- [[DataVis-2026-02_Perception.pdf|Perception lecture slides]].

