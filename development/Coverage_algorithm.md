---
title: Missing Region Detection Algorithm
layout: default
parent: Development Overview
has_toc: true
---

# Missing Region Detection Algorithm

> 📖 For the research behind this algorithm, see the [Research](./scan_gap_detection_research.md)

This algorithm is responsible for identifying incomplete scan regions during the scanning process. A screenshot of the scanning software is captured and processed using computer vision techniques to detect areas that have not been fully scanned.

## Pipeline Overview
 
The algorithm runs as a sequential pipeline — each stage feeds directly into the next:
 
```
Raw Screenshot → ROI Crop → HSV Masking → Marker Detection → Gap Detection
```

## Stage 1 — Colour Space Conversion & Masking
 
The raw ROI is converted from BGR to HSV before any analysis. HSV isolates hue independently of brightness, making colour detection far more robust under varying lighting conditions.
 
```python
hsv = cv2.cvtColor(cropped, cv2.COLOR_BGR2HSV)
 
lower_blue = np.array([90, 40, 40], dtype=np.uint8)
upper_blue = np.array([130, 255, 255], dtype=np.uint8)
blue_mask = cv2.inRange(hsv, lower_blue, upper_blue)
```
 
This produces a binary mask. white, where scanned surface data exists, black everywhere else. A Gaussian blur is then applied to smooth out noise before thresholding:

```python
blurred = cv2.GaussianBlur(blue_mask, (31, 31), 0)
good_mask = blurred > thresh
bad_mask = blurred <= thresh
```
 
`good_mask` represents the correctly scanned regions. `bad_mask` is the set of pixels the rest of the algorithm focuses on — the gaps.

---
 
## Stage 2 — Marker Detection & Circularity Filtering
 
Both white and red markers are detected using the same core approach. For white markers, a simple grayscale threshold is used. For red markers, two HSV ranges are combined to catch red's wrap around at both ends of the hue spectrum:

```python
lower_red1 = np.array([0, 100, 100], dtype=np.uint8)
upper_red1 = np.array([10, 255, 255], dtype=np.uint8)
lower_red2 = np.array([170, 100, 100], dtype=np.uint8)
upper_red2 = np.array([180, 255, 255], dtype=np.uint8)
 
red_mask = cv2.bitwise_or(
    cv2.inRange(hsv, lower_red1, upper_red1),
    cv2.inRange(hsv, lower_red2, upper_red2)
)
```
 
Contours are then extracted and filtered using a circularity score to distinguish the round reference markers from noise or edges:
 
```python
circularity = 4 * np.pi * area / (perimeter ** 2)
if circularity > 0.6:
    # valid marker
```
 
A perfect circle has a circularity of `1.0`. The `0.6` threshold is used to catch imperfect marker captures while ignoring irregular shapes. Each valid marker's centroid is recorded using image moments:
 
```python
M = cv2.moments(contour)
cx = int(M["m10"] / M["m00"])
cy = int(M["m01"] / M["m00"])
```
 
Both white and red marker masks are used as exclusion zones, preventing the algorithm from flagging the markers themselves as unscanned gaps.
 
---

## Stage 3 — Unscanned Region Detection & Distance Filtering
 
The core detection logic takes the `bad_mask`, strips out the marker zones, then analyses what remains:
 
```python
unscanned_regions = bad_mask.astype(np.uint8) * 255
unscanned_regions = cv2.bitwise_and(unscanned_regions, cv2.bitwise_not(white_exclusion_mask))
unscanned_regions = cv2.bitwise_and(unscanned_regions, cv2.bitwise_not(red_exclusion_mask))
```
 
Contours of remaining unscanned areas are found and filtered by minimum area to ignore tiny gaps:
 
```python
contours, _ = cv2.findContours(unscanned_regions, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
 
for contour in contours:
    area = cv2.contourArea(contour)
    if area < min_area:
        continue
```
 
For each valid region, the distance to the nearest marker is computed and checked against tuned parameters:
 
```python
for mx, my in all_marker_centers:
    d = np.sqrt((cx - mx) ** 2 + (cy - my) ** 2)
    if d < min_dist:
        min_dist = d
 
if min_distance <= min_dist <= max_distance:
    scan_points.append((cx, cy, x_cent, y_cent))
```
 
This is important as the algorithm is only flagging gaps that sit within a sensible distance from a known marker. Too far away and its likely the background or image noise and if its too close then the "missing gap" is likely overlapping with the markers.

The centroid cooordinates are returned relative to the ROI centre which is practical for converting pixel positions into trajectories for the robotic arm.
 
### Example video of the algorithm 

The debugging UI shows the scanned surface in green and unscanned regions in red. A purple crosshair marks the detected gap with its coordinates. This UI is only used for debugging and tuning parameters to make sure that the algorithm is operating correctly.

<iframe width="560" height="315" src="https://www.youtube.com/embed/FD7EJdslvGY?si=9DmhB79fQZ5ftJNe" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---
 
## Tuning Parameters
 
```python
find_unscanned_near_markers(
    ...
    max_distance=50,  # how far a gap can be from a marker (pixels)
    min_distance=0,   # how close a gap can be to a marker (pixels)
    min_area=100      # minimum pixel area to count as a real gap
)
```
 
These three values are used for tuning when scanning different objects and under different scan conditions. Changing these parameters will effect how many gaps are found and if they are close to the object.
 
