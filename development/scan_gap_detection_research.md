---
title: Research for the missing region detection
layout: default
parent: Development Overview
has_toc: true
---

# Research for the Missing Region Detection Algorithm

This page covers the core computer vision concepts underpinning the algorithm used for detecting gaps and missing regions.

---

## HSV Colour Space & Image Segmentation

### What is HSV?

RGB (Red, Green, Blue) is the default colour format for most images each pixel is represented as a mix of three colour channels. While RGB is intuitive, it is poorly suited for colour based detection because brightness and colour are mixed together. A bright blue and a dark blue will have very different RGB values despite being the same hue.

HSV solves this by separating colour into three independent components:

- **Hue** — the colour (0–180° in OpenCV)
- **Saturation** — how washed out the colour is
- **Value** — how bright or dark the colour is

This means a specific hue can be isolated regardless of lighting conditions, making HSV more robust[^1] [^2].

---

## Gaussian Blur & Thresholding

### What is Gaussian Blur?

Gaussian blur is a low-pass filter that smooths an image by replacing each pixel with a weighted average of its surrounding area. Pixels closer to the centre contribute more weight where the weighting follows a Gaussian distribution

In OpenCV this is applied with:

```python
blurred = cv2.GaussianBlur(blue_mask, (31, 31), 0)
```

The `(31, 31)` parameter is the kernel size. This means a 31×31 pixel area is considered for each pixel [^1] [^3].

### Why blur before thresholding?

The raw binary mask from `inRange` is noisy, small spreads of blue appear in regions that aren't truly scanned, and small gaps appear within regions that are. Applying a Gaussian blur before thresholding smooths these out merging nearby noise into the surrounding region.

### What is Thresholding?

Thresholding converts a grayscale image into a binary (black/white) image by comparing each pixel value against a cutoff [^1] [^4]. Pixels above the threshold are set to one value and pixels below are set to another.

```python
good_mask = blurred > thresh
bad_mask  = blurred <= thresh
```

After blurring, the pixel intensity at any given point reflects how densely blue the surrounding region is. A high value means it's surrounded by scanned data, a low value means it's missing. The `thresh` parameter controls how strict this classification is.

---

## Contour Detection & Circularity Filtering

### What are Contours?

A contour is the boundary of a connected region in a binary image this is essentially the outline of a shape. OpenCV's `findContours` function extracts these boundaries, which can then be analysed for shape properties like area, perimeter, and position [^1] [^5].

```python
contours, _ = cv2.findContours(unscanned_regions, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
```

`RETR_EXTERNAL` retrieves only the outermost contours. `CHAIN_APPROX_SIMPLE` compresses horizontal, vertical, and diagonal segments, storing only their endpoints.

### Circularity Filtering

Not every contour in the image corresponds to a real reference marker. Since edges, reflections, and noise all produce contours. Circularity is used to filter these out as it measures how close a shape is to a circle, scored from 0 to 1:

```
circularity = (4 × π × area) / perimeter²
```

A perfect circle scores 1.0. Irregular shapes like edges or sharp shapes score much lower [^5]. The algorithm uses a threshold of 0.6 to accept only roughly circular shapes:

```python
circularity = 4 * np.pi * area / (perimeter ** 2)
if circularity > 0.6:
```

### Image Moments & Centroid Calculation

Once a valid contour is confirmed, its centre point (centroid) is calculated using image moments. Moments are weighted averages of pixel intensities across the contour [^6]. The zeroth moment (`m00`) gives the area, and the first order moments (`m10`, `m01`) give the weighted x and y positions.

The centroid is calculated as:

```
Cx = m10 / m00
Cy = m01 / m00
```

In code:

```python
M = cv2.moments(contour)
cx = int(M["m10"] / M["m00"])
cy = int(M["m01"] / M["m00"])
```

This gives the exact centre of each detected marker, which is then used as a reference point for the distance filtering stage.

---

## References
[^1]: G. Bradski and A. Kaehler, *Learning OpenCV: Computer Vision with the OpenCV Library*, O'Reilly Media, 2008.
[^2]: OpenCV Documentation. “Changing Colorspaces.” Available at: https://docs.opencv.org/4.x/df/d9d/tutorial_py_colorspaces.html
[^3]: OpenCV Documentation. “Smoothing Images (Image Filtering).” Available at: https://docs.opencv.org/4.x/d4/d13/tutorial_py_filtering.html
[^4]: OpenCV Documentation. “Image Thresholding.” Available at: https://docs.opencv.org/4.x/d7/d4d/tutorial_py_thresholding.html
[^5]: OpenCV Documentation. “Contour Features.” Available at: https://docs.opencv.org/4.x/dd/d49/tutorial_py_contour_features.html
[^6]: OpenCV Documentation. “Image Moments.” Available at: https://docs.opencv.org/4.x/d0/d49/tutorial_moments.html