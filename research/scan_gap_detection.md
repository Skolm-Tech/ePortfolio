---
title: Research for the missing region detection
layout: default
parent: Research Overview
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

This means a specific hue can be isolated regardless of lighting conditions, making HSV far more robust

> **Further reading:** [OpenCV Colour Space Conversion](https://docs.opencv.org/4.x/df/d9d/tutorial_py_colorspaces.html)
> **Reference:** Bradski, G. & Kaehler, A. (2008). *Learning OpenCV*. O'Reilly Media. — Chapter 3: Getting to know OpenCV

### How it's used in the algorithm

The scan viewport in EXScan HX renders scanned surface data as a consistent blue colour. By converting the ROI to HSV and isolating a specific hue range, the algorithm can extract a binary mask of scanned vs unscanned regions regardless of brightness.

```python
hsv = cv2.cvtColor(cropped, cv2.COLOR_BGR2HSV)

lower_blue = np.array([90, 40, 40], dtype=np.uint8)
upper_blue = np.array([130, 255, 255], dtype=np.uint8)
blue_mask = cv2.inRange(hsv, lower_blue, upper_blue)
```

The `inRange` function produces a binary mask white (255) where pixels fall within the defined hue range, black (0) everywhere else.

Red is a special case because it wraps around the HSV hue circle, it exists at both the low end (0–10°) and the high end (170–180°) of the spectrum. Two separate masks are therefore created

```python
red_mask = cv2.bitwise_or(
    cv2.inRange(hsv, lower_red1, upper_red1),
    cv2.inRange(hsv, lower_red2, upper_red2)
)
```

---

## Gaussian Blur & Thresholding

### What is Gaussian Blur?

Gaussian blur is a low-pass filter that smooths an image by replacing each pixel with a weighted average of its surrounding area. Pixels closer to the centre contribute more weight where the weighting follows a Gaussian distribution

In OpenCV this is applied with:

```python
blurred = cv2.GaussianBlur(blue_mask, (31, 31), 0)
```

The `(31, 31)` parameter is the kernel size. This means a 31×31 pixel area is considered for each pixel.

> **Further reading:** [OpenCV Smoothing Images](https://docs.opencv.org/4.x/d4/d13/tutorial_py_filtering.html) | [OpenCV Image Thresholding](https://docs.opencv.org/4.x/d7/d4d/tutorial_py_thresholding.html)
> **Reference:** Bradski, G. & Kaehler, A. (2008). *Learning OpenCV*. O'Reilly Media. — Chapter 5: Image Processing

### Why blur before thresholding?

The raw binary mask from `inRange` is noisy, small spreads of blue appear in regions that aren't truly scanned, and small gaps appear within regions that are. Applying a Gaussian blur before thresholding smooths these out merging nearby noise into the surrounding region.

### What is Thresholding?

Thresholding converts a grayscale image into a binary (black/white) image by comparing each pixel value against a cutoff. Pixels above the threshold are set to one value; pixels below are set to another.

```python
good_mask = blurred > thresh
bad_mask  = blurred <= thresh
```

After blurring, the pixel intensity at any given point reflects how densely blue the surrounding region is. A high value means it's surrounded by scanned data — a low value means it's missing. The `thresh` parameter controls how strict this classification is.

> **Further reading:** [OpenCV Smoothing Images](https://docs.opencv.org/4.x/d4/d13/tutorial_py_filtering.html) | [OpenCV Image Thresholding](https://docs.opencv.org/4.x/d7/d4d/tutorial_py_thresholding.html)
> **Reference:** Bradski, G. & Kaehler, A. (2008). *Learning OpenCV*. O'Reilly Media. — Chapter 5: Image Processing

---

## Contour Detection & Circularity Filtering

### What are Contours?

A contour is the boundary of a connected region in a binary image this is essentially the outline of a shape. OpenCV's `findContours` function extracts these boundaries, which can then be analysed for shape properties like area, perimeter, and position.

```python
contours, _ = cv2.findContours(unscanned_regions, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
```

`RETR_EXTERNAL` retrieves only the outermost contours. `CHAIN_APPROX_SIMPLE` compresses horizontal, vertical, and diagonal segments, storing only their endpoints.

> **Further reading:** [OpenCV Contour Features](https://docs.opencv.org/4.x/dd/d49/tutorial_py_contour_features.html) | [Contour Detection — LearnOpenCV](https://learnopencv.com/contour-detection-using-opencv-python-c/)
> **Reference:** Bradski, G. & Kaehler, A. (2008). *Learning OpenCV*. O'Reilly Media. — Chapter 8: Contours

### Circularity Filtering

Not every contour in the image corresponds to a real reference marker. Since edges, reflections, and noise all produce contours. Circularity is used to filter these out as it measures how close a shape is to a circle, scored from 0 to 1:

```
circularity = (4 × π × area) / perimeter²
```

A perfect circle scores 1.0. Irregular shapes like edges or sharp shapes score much lower. The algorithm uses a threshold of 0.6 to accept only roughly circular shapes:

```python
circularity = 4 * np.pi * area / (perimeter ** 2)
if circularity > 0.6:
```

### Image Moments & Centroid Calculation

Once a valid contour is confirmed, its centre point (centroid) is calculated using image moments. Moments are weighted averages of pixel intensities across the contour. The zeroth moment (`m00`) gives the area, and the first order moments (`m10`, `m01`) give the weighted x and y positions.

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

> **Further reading:** [OpenCV Image Moments Tutorial](https://docs.opencv.org/4.x/d0/d49/tutorial_moments.html) 
> **Reference:** Bradski, G. & Kaehler, A. (2008). *Learning OpenCV*. O'Reilly Media. — Chapter 8: Contours (covers moments)

---

## Gap Detection & Distance Filtering

### Finding Unscanned Regions

Once the `bad_mask` has been cleaned up and marker exclusion zones removed, `findContours` is ran on what remains. Each contour at this point is a possible unscanned gap.

However, not all of these regions are useful. Small spreads of noise, artefacts at the frame edge, and regions far from any reference marker are all irrelevant to object being scanned. Two filtering steps remove these:

**Area filtering**  any contour below a minimum pixel area is discarded outright:
```python
if area < min_area:
    continue
```

**Distance filtering** for each remaining region, the distance to every known marker centre is computed, and only the shortest is kept:
```python
for mx, my in all_marker_centers:
    d = np.sqrt((cx - mx) ** 2 + (cy - my) ** 2)
    if d < min_dist:
        min_dist = d
```

Only regions that fall within a specified distance band around a marker are flagged:
```python
if min_distance <= min_dist <= max_distance:
    scan_points.append((cx, cy, x_cent, y_cent))
```

### Why Distance to Markers?

The physical reference markers on the object are used to reference where the object is for the 3D scanner. This means any unscanned gap that is close to a marker is a gap that is within the object. Gaps far from any marker are either noise or are gaps within ground on which the object is on.

This makes the distance the most important tuning parameter in the whole algorithm as it defines what counts as a "reachable and relevant" for the current scan position.

