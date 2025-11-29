---
layout: default
title: Development Progress
parent: Development
has_toc: true
---

# Development Progress

This page documents the active development for the project such as videos, explanations, and images of all the progress.

## Scanner Preperation & Object selection

<!-- TODO: add pages regarding the object and scanner -->
The first step was setting up the [Einscan HX](Scanner.md) using the proprietary shining 3D software. The software handles the laser scanning process and the calibration.

Secondly an [Object](Object.md) was chosen which fit the criteria for this project such as:

- Complex gemoetry
- Metatllic surface 
- Suitable size for scanning
- Multiple hidden regions
- Industrial component

<img src="media/object1.png" width="400">

Retro reflective markers were placed on the object surface. These markers allow the software to identify the object for laser scanning.

## Inital Scanning Process

Multiple 360° scans were performed using the Einscan HX scanner on the object with different quality settings such as high quality, medium quality and low quality. The difference in quality is based on point spacing. For example, high detail has small point spacing (0.1 - 0.2 mm) which means there is less distance between points, creating a denser point cloud.

![quality](media/quality.png)

### High quality scan

<!-- TODO: move scans to dedicated markdown page for better organization (goal is to only have 1 example video to stop clogging this page) -->
<!-- Embeds should work on website -->
<iframe width="560" height="315" src="https://www.youtube.com/embed/AgiyfbiQujc?si=6LhLRWmq3dH_4au9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Medium quality scan
<iframe width="560" height="315" src="https://www.youtube.com/embed/7uTEE94zOBQ?si=_MC891vxYdaOZJzS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Low quality scan
<iframe width="560" height="315" src="https://www.youtube.com/embed/84lJhVLyIkc?si=NXDlwlj8titRoRPp" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Very Low quality scan

<iframe width="560" height="315" src="https://www.youtube.com/embed/0h9UlIu9WmU?si=OCUoKQ2Qpq41Lr0N" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<!-- subject to change -->
## Image-Based Coverage Analysis 
The EinScan HX software does not allow direct access to the 3D point cloud data during real time. To work around this limitation, we implement image-based coverage analysis which uses the visual output of the software to assess the surface coverage and scan quality. More information regarding this approach can be found [here](Coverage_algorithm.md)

<iframe width="560" height="315" src="https://www.youtube.com/embed/uqpih3dPnRI?si=mgQZfApDY_zXePSC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The video shows a comparison between the normal scan and the algorithm processed scan. Due to the additional processing, the comparions may be out sync despite attempts to match the speeds of both videos

In the processed scan:
- Green regions indicate the area is successfully scanned.
- Red regions indicate the area is missing/low density.

A threshhold slider is also included to adjust the threshhold of how strictly the algorithm classifies the regions as scanned (green) or missing/low density (red).

Known Limitations:
- Background is detected as not scanned
- Floor is detected as scanned
- Object is not centered in the frame
- Processing is slower than real time