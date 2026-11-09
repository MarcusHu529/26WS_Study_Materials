# Computer Vision I — Exercise 1 Notes

OpenCV, C++ and translating image-processing algorithms into code

Practical notes on **how to implement image-processing concepts in OpenCV**, based on the exercise conversation. Related lecture theory: [[Operators]].

Code snippets use the exercise's 8-bit grayscale images and integer histogram counts. They are examples to use inside the relevant functions, not one standalone program.

## 1. `cv::Mat` — The most important OpenCV class

`cv::Mat` stores an image or a matrix of numerical data.

### Creating and loading images

```cpp
// Load an image as 8-bit grayscale
cv::Mat image = cv::imread("image.jpg", cv::IMREAD_GRAYSCALE);

// Create a black grayscale image
cv::Mat output = cv::Mat::zeros(image.rows, image.cols, CV_8UC1);

// Create a black BGR drawing canvas
cv::Mat canvas = cv::Mat::zeros(400, 512, CV_8UC3);
```

### Essential properties

| Expression | Meaning |
|--------|--------|
| `image.rows` | Image height |
| `image.cols` | Image width |
| `image.empty()` | Check whether image data exists |
| `CV_8UC1` | One-channel unsigned 8-bit image; grayscale values 0–255 |
| `CV_8UC3` | Three-channel 8-bit image |
| `CV_32S` | Signed 32-bit integer depth; used here for one-channel counts |
| `uchar` | Unsigned 8-bit type used to access these grayscale pixels, 0–255 |

**Important:** In OpenCV, pixel access follows `image.at<Type>(row, col)`, not `(x, y)`.

```cpp
uchar pixel = image.at<uchar>(row, col);

// Modify the pixel
image.at<uchar>(row, col) = 150;
```

The template type must match the matrix's element type: use `at<uchar>()` for `CV_8UC1` and `at<int>()` for the exercise's `CV_32S` counts. A `CV_8UC3` pixel has three components and uses `at<cv::Vec3b>()`.

`CV_8U` specifies the unsigned 8-bit depth. In the matrix creation calls used in your exercise, it is equivalent to the explicit one-channel type `CV_8UC1`.

**Coordinate translation:** A lecture pixel $f(x,y)$ is read as `image.at<uchar>(y, x)`: row first, column second.

---

## 2. Translating a pixel operation into C++

For an image-processing formula such as:

$$
g(x,y)=255-f(x,y)
$$

Interpret it as:

- Read an original pixel.
- Perform the calculation.
- Store the result at the same location.

### General pattern

```cpp
for (int row = 0; row < image.rows; ++row) {
    for (int col = 0; col < image.cols; ++col) {

        uchar pixel = image.at<uchar>(row, col);

        uchar new_pixel = 255 - pixel;

        output.at<uchar>(row, col) = new_pixel;
    }
}
```

This structure can be reused for brightness adjustments, thresholding, image inversion, and applying an intensity lookup table.

**Key idea:** The mathematical expression determines what happens to a pixel. The nested loops determine where and how often the operation is applied.

---

## 3. Histograms and CDCs — How to store numerical results

A histogram does not need to be an image. It is an array of frequency counts.

### Histogram

```cpp
cv::Mat histogram = cv::Mat::zeros(1, 256, CV_32S);
```

Each column represents one grayscale intensity.

For example:

```cpp
histogram.at<int>(0, 100)
```

returns the number of pixels with intensity 100.

To update the frequency:

```cpp
histogram.at<int>(0, pixel)++;
```

### CDC: cumulative counts

In your code, the matrix called `cdc` stores **cumulative counts**:

$$
C(k)=\sum_{i=0}^{k}h(i).
$$

It has the same shape as the histogram. The normalized cumulative distribution from the lecture is $H(k)=C(k)/N$, where $N$ is the number of pixels. Thus `cdc` contains counts from $0$ to $N$, not probabilities from $0$ to $1$.

```cpp
cv::Mat cdc = cv::Mat::zeros(1, 256, CV_32S);

cdc.at<int>(0, 0) = histogram.at<int>(0, 0);

for (int k = 1; k < 256; ++k) {
    cdc.at<int>(0, k) =
        cdc.at<int>(0, k - 1) + histogram.at<int>(0, k);
}
```

Useful verification:

```cpp
int total_pixels = cdc.at<int>(0, 255);
```

This should equal:

```cpp
image.total()
```

The histogram bins must sum to the same number, and the CDC must never decrease. Equalization changes intensities without changing the number of pixels, so original and equalized CDCs have the same last count. The `CV_32S` counts used here assume the total fits in a signed 32-bit integer.

### Important distinction

| Matrix | Data stored | Typical type |
|--------|--------|--------|
| `image` | Individual pixel intensities | `CV_8UC1` |
| `histogram` | Frequency per intensity | `CV_32S` |
| `cdc` | Cumulative frequency | `CV_32S` |
| `histogram_image` | Rendered chart pixels | `CV_8UC3` |

**Remember:** The numerical histogram is not the same as the picture of a histogram.

---

## 4. Drawing with OpenCV

OpenCV provides drawing operations that modify an image.

| Function | Use |
|--------|--------|
| `cv::rectangle()` | Draw histogram bars |
| `cv::line()` | Draw CDC curves |
| `cv::Scalar(B,G,R)` | Specify a color |
| `cv::FILLED` | Draw a filled shape |
| `cv::imshow()` | Display the resulting image |

### Histogram bars

```cpp
cv::rectangle(
    canvas,
    top_left,
    bottom_right,
    cv::Scalar(255, 255, 255),
    cv::FILLED
);
```

### CDC curve

```cpp
cv::line(
    canvas,
    previous_point,
    current_point,
    cv::Scalar(255, 255, 255),
    1
);
```

### Image coordinates

The image origin is at the top-left. Positive X points right; positive Y points down. Drawing uses `cv::Point(x, y)`, while matrix access uses `(row, col)`.

For a filled histogram bar, calculate an integer `bar_height` between $0$ and the canvas height. Skip zero-height bars, then draw:

```cpp
int height = canvas.rows;
int bar_height = static_cast<int>(
    static_cast<double>(frequency) / max_frequency * height
);

if (bar_height > 0) {
    cv::rectangle(
        canvas,
        cv::Point(2 * k, height - bar_height),
        cv::Point(2 * k + 1, height - 1),
        cv::Scalar(255, 255, 255),
        cv::FILLED
    );
}
```

This assumes a positive `max_frequency` and a 512-pixel-wide canvas: each of the 256 bins occupies two columns.

For CDC curve points, map a normalized value $p=C(k)/N$ into the valid coordinate range:

$$
y=(H_{\text{canvas}}-1)(1-p).
$$

```cpp
double p = static_cast<double>(cdc.at<int>(0, k)) / total_pixels;
int y = static_cast<int>(
    std::round((canvas.rows - 1) * (1.0 - p))
);
cv::Point point(2 * k, y);
```

Here `total_pixels` must be positive. For a 400-row canvas, $p=0$ maps to row $399$ and $p=1$ to row $0$. Using `rows - 1` fixes the boundary issue in your CDC drawing: `rows - 0` would give row $400$, outside the image.

---

## 5. Applying histogram equalization in code

The main formula you implemented was:

$$
T(k)=\operatorname{round}\left(255\frac{C(k)}{N}\right)
$$

The histogram and CDC must come from the same image being transformed. The essential coding pattern is:

```cpp
uchar original_intensity = image.at<uchar>(row, col);

double new_intensity =
    255.0 * cdc.at<int>(0, original_intensity)
    / cdc.at<int>(0, 255);

uchar rounded_new_intensity =
    static_cast<uchar>(std::round(new_intensity));

equalized_image.at<uchar>(row, col) = rounded_new_intensity;
```

### The C++ lesson: Type conversion matters

This expression is problematic:

```cpp
static_cast<float>(3 / 10)
```

The division occurs before conversion, producing `0.0`.

Instead:

```cpp
static_cast<float>(3) / 10
```

produces `0.3`.

For pixel operations, calculate in a suitable numerical type, round when required, and only then convert to `uchar`. With a valid CDC and a positive total, this formula stays within 0–255. For other operations that can go outside that range, clamp the result or use `cv::saturate_cast<uchar>()`; an ordinary cast does not provide clipping.

OpenCV also offers the built-in alternative:

```cpp
cv::Mat result;
cv::equalizeHist(image, result);
```

The input to `cv::equalizeHist()` must be a nonempty, single-channel unsigned 8-bit image. Its normalization accounts for the first occupied histogram bin, unlike the basic $255C(k)/N$ mapping used here. The two outputs can therefore differ without your manual formula being wrong.

**Quick comparison:** With intensities $[100,200]$, your formula gives $[128,255]$; OpenCV maps them to $[0,255]$. OpenCV also handles constant images separately. [OpenCV implementation](https://github.com/opencv/opencv/blob/4.12.0/modules/imgproc/src/histogram.cpp)

---

## 6. Function design — A useful C++ pattern

Your exercise used functions such as:

```cpp
void calculate_CDC(
    const cv::Mat& histogram,
    cv::Mat& cdc
);
```

The parameters have different roles:

| Parameter | Purpose |
|--------|--------|
| `const cv::Mat& histogram` | Input, read-only |
| `cv::Mat& cdc` | Output, modified by the function |
| `void` | No return value |

Usage:

```cpp
cv::Mat cdc;
calculate_CDC(histogram, cdc);
```

The function writes into `cdc` through the reference.

This is why the following pattern is wrong for a `void` function:

```cpp
cv::Mat cdc = calculate_CDC(histogram, cdc);
```

**Programming habit:** Separate calculation functions from visualization functions. That lets you reuse the same algorithm with different images and window titles.

---

## 7. Showing images and debugging

### Displaying

```cpp
cv::imshow("Original Image", image);
cv::imshow("Equalized Image", equalized_image);
cv::waitKey(0);
```

`imshow()` displays an image in a named window. Calling it again with the same window name updates that window.

`waitKey(0)` waits for a keypress and allows OpenCV's window events to be processed. Use it after `imshow()` so the windows can update and remain available.

**Your low-contrast image issue:** `imread()` loads data but does not display it. After checking `low_contrast_image.empty()`, add:

```cpp
cv::imshow("Original Low Contrast Image", low_contrast_image);
```

Your `wait_for_and_process_key_input()` helper uses **Space** to continue and **Esc** to quit. Later images, including Task 4's OpenCV result, appear only after execution reaches those calls.

### Common errors from this exercise

| Problem | What to check |
|--------|--------|
| Image does not appear | Did you call `imshow()`? |
| Image cannot be loaded | Check `image.empty()` and the file path; relative paths start at the program's working directory |
| Compile error with `.at()` | Specify the type, e.g. `.at<uchar>()` |
| Incorrect pixel values | Check integer versus floating-point division |
| Wrong graph orientation | Check OpenCV's downward Y-axis |
| `imshow()` compile error | Pass an image matrix, not a function |
| Window gets replaced | Use different window names |
| Program exits before `main()` | Check runtime DLL availability and `PATH` |

For a Windows DLL-loading problem, make sure the required OpenCV runtime binaries are discoverable, for example through the correct OpenCV `bin` directory on the launched program's `PATH`.

---

## 8. A workflow for the next exercise

1. Identify the input and output: image, counts, mapping, or drawing canvas.
2. Choose each matrix's dimensions and element type.
3. Translate the formula into reading values, calculating, and assigning results.
4. Check simple invariants: valid intensity range, histogram total, and CDC endpoint.
5. Display the result separately with a clear window name and compare it with the input.

For this exercise, the data flow is:

```text
Grayscale image -> Histogram counts -> Cumulative counts -> Intensity mapping
                                                            |
                                                            v
                                                      Equalized image
                                                            |
                                                            v
                                                   New histogram and CDC
```

## Sources

- [Plan computer vision exercise conversation](chatgpt-conversation://6af097f7-eb8c-83eb-b036-57bb0746297a), especially the final practical notes and code review.
- [[Point Operators and Histogram Equalization - Exercise 01.pdf|Exercise sheet]].
- [OpenCV matrix reference](https://docs.opencv.org/4.12.0/d3/d63/classcv_1_1Mat.html) and [window/display reference](https://docs.opencv.org/4.12.0/d7/dfc/group__highgui.html).
