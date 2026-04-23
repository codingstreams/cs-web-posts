Interpolation is one of the most important concepts in computer vision
and image processing. If you work with OpenCV, you use interpolation
every time you resize, rotate, or transform an image - even if you
don't realize it.

This guide covers: - What interpolation means

- Why OpenCV uses interpolation
- Types of interpolation
- Practical code examples

------------------------------------------------------------------------

## What Is Interpolation?

Interpolation is the process of estimating pixel values between known
pixels.

When an image is transformed (resized, rotated, zoomed), some pixel
positions in the output have no direct mapping from the input.
Interpolation helps estimate those missing pixel values.

In simple terms: Interpolation fills the gaps when an image is resized
or transformed.

------------------------------------------------------------------------

## Why Is Interpolation Needed in OpenCV?

OpenCV uses interpolation in:

- Image resizing
- Image rotation
- Affine transform
- Perspective transform
- Image warping
- Zooming in/out

Without interpolation, images would appear blocky, torn, or low-quality.

------------------------------------------------------------------------

## Types of Interpolation in OpenCV (With Examples)

Below are the main interpolation methods with sample code and
explanations.

------------------------------------------------------------------------

### 1. INTER_NEAREST - Nearest Neighbor

Picks the closest pixel value.

- Fastest
- Pixelated/blocky output
- Best for segmentation masks & labels

``` python
import cv2

img = cv2.imread("image.jpg")
nearest = cv2.resize(img, None, fx=3, fy=3, interpolation=cv2.INTER_NEAREST)
cv2.imwrite("nearest_output.jpg", nearest)
```

------------------------------------------------------------------------

### 2. INTER_LINEAR - Bilinear Interpolation (Default)

Interpolates using weighted average of 4 neighboring pixels.

- Good balance of quality and speed
- Default in most OpenCV functions

``` python
linear = cv2.resize(img, None, fx=3, fy=3, interpolation=cv2.INTER_LINEAR)
cv2.imwrite("linear_output.jpg", linear)
```

------------------------------------------------------------------------

### 3. INTER_CUBIC - Bicubic Interpolation

Uses 16 neighboring pixels for smoother results.

- High-quality upscaling
- Slower than linear

``` python
cubic = cv2.resize(img, None, fx=3, fy=3, interpolation=cv2.INTER_CUBIC)
cv2.imwrite("cubic_output.jpg", cubic)
```

------------------------------------------------------------------------

### 4. INTER_AREA - Best for Downscaling

Averages pixel values over an area for clean shrinking.

- Sharp output when reducing image size
- Avoids aliasing

``` python
area = cv2.resize(img, None, fx=0.3, fy=0.3, interpolation=cv2.INTER_AREA)
cv2.imwrite("area_output.jpg", area)
```

------------------------------------------------------------------------

### 5. INTER_LANCZOS4 - High-Quality Resampling

Uses a Lanczos kernel with 8×8 neighborhood.

- Highest quality
- Slowest
- Best for photography, editing, printing

``` python
lanczos = cv2.resize(img, None, fx=3, fy=3, interpolation=cv2.INTER_LANCZOS4)
cv2.imwrite("lanczos_output.jpg", lanczos)
```

------------------------------------------------------------------------

## Full Example: Comparing All Methods

``` python
import cv2

img = cv2.imread("input.jpg")

methods = {
    "nearest": cv2.INTER_NEAREST,
    "linear": cv2.INTER_LINEAR,
    "cubic": cv2.INTER_CUBIC,
    "area": cv2.INTER_AREA,
    "lanczos": cv2.INTER_LANCZOS4
}

for name, method in methods.items():
    out = cv2.resize(img, None, fx=2, fy=2, interpolation=method)
    cv2.imwrite(f"output_{name}.jpg", out)
```

------------------------------------------------------------------------

## Summary Table

|Interpolation Method |  Best Use Case        |  Quality    | Speed|
| ---------------------- |---------------------- |----------- |----------|
|INTER_NEAREST|Segmentation masks|Low|Very High|
|INTER_LINEAR|General resizing|Medium|High|
|INTER_CUBIC|Upscaling photos|High|Medium|
|INTER_AREA|Downscaling images|High|High|
|INTER_LANCZOS4|High-quality editing|Very High|Low|

------------------------------------------------------------------------

Interpolation is essential for clean image resizing, smooth rotations, and high-quality transformations. OpenCV provides multiple interpolation options so you can choose the best one based on your task.
