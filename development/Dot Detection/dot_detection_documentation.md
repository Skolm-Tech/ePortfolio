---
title: Dot Detection Code Documentation
layout: default
parent: Development Overview
has_toc: true
---

# dot_detection.py Documentation

**Class:** <br>
`DotDetect()`

**Variables:** <br>
`self.dots` = [ID, x_original, y_original, x, y, radius, angle] array containing the current list of dot ID's and coordinates <br>
`self.new_dots` = [x_original, y_original, x, y, radius, angle] array containing dots identified in the new image <br>
`self.old_dots` = [x_original, y_original, x, y, radius, angle] array contating dots identified in the previous image <br>
`self.first_image` = Boolean indicates whether this is the first image <br>
`self.id_count` = integer value for the next dot ID, adds one per dot identified in total <br>
`self.max_radius` = maximum possible radius change between two dots, needs to be updated when test examples done <br>
`self.max_angle` =  maximum possible angle change between two dots, needs to be updated when test examples done <br>
`self.cutoff` = max possible radius and angle change possible anything higher than this is ignored

**Methods:**  <br>
`self.hungarian_algorithm(mat)`-
The functions within this region are purely for running the HAA so won't be needed outside this file. <br>
Inputs = a x a matrix for the HAA to be run on <br>
Outputs = ans_pos list of tuples giving the matching pairs i.e. (row 1, column 3) etc. <br>
Process = runs the HAA algorithm to find the best set of matches

`self.find_dots(image)`- <br>
Inputs = image np.ndarray, pass the image as an array frame <br>
Outputs = None <br>
Process = identifies the reflective dots in an image array, lists them in an array as coordinate representations of pixel xy, central xy, and polar coordinates

`self.track_dots()`- <br>
Inputs = None <br>
Outputs = None <br>
Process = the current dot array is moved into prev_dots, if this is the first instance all dots in new_dots are added to current_dots and ID'd, if not the function finds the position between each new and previous dot and uses the hungarian assignment algorithm to find the best set of matches, it then updates this list of matches removing lost ones, updating available ones, and adding appearing dots. The updated list is passed into self.current_dots

`self.reset()`- <br>
Inputs = None <br>
Outputs = None <br>
Process = reverts current_dots, prev_dots, new_dots to their initial condition and resets the first_image and id_count to be used for when the camera has moved a lot but you don't want to reinitialise the class