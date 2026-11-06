## What is a Digital Image:
- In computer vision, an image is simply a **lookup table or grid** that pairs every coordinate with a color.
	- **Image Dimensions** ($n_0 \times n_1$): $n_0$​ is the width (number of columns) and $n_1$​ is the height (number of rows).
	- **Pixel Coordinate:** Any location $(x, y)$ on the grid.
	- **Color Set ($C$):** The palette of allowed values a pixel can have (e.g., shades of gray or RGB values).
	- **Image Function ($f$):** A rule $f(x, y) = \text{color}$ that assigns a color from palette $C$ to coordinate $(x, y)$.
	- 
## Finite vs. Infinite Images
- **Digital Image ($f \in C^{n_0 \times n_1}$):** A standard image with a fixed grid size of $n_0$ pixels wide by $n_1$ pixels high.
- **Infinite Digital Image ($f \in C^{\mathbb{Z} \times \mathbb{Z}}$):** An image with an endless grid extending forever in all directions across integer coordinates.
## Pixel Grid Graph (4-Neighbor Connectivity)
- To mathematically define which pixels are next to each other, the image grid is treated as a graph $G = (V, E)$:
	- **Vertices ($V$):** Every pixel coordinate on the grid.
	- **Edges ($E$):** Two pixels $r$ and $r′$ are connected by an edge if their distance $\Vert{}r - r'\Vert{}$ equals 1.
	- **What this means:** A pixel is only connected to its 4 immediate orthogonal neighbors (top, bottom, left, and right). Diagonal pixels are distance $\sqrt{2} \approx 1.414$, so they are not directly connected.

