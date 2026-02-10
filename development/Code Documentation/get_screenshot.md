# get_screenshot.py Documentation

**Class:** <br>
`SystemScreenshot()`

**Variables:** <br>
`self.roi` = [height, width] gives the size in pixels of the region of interest around the centre of the screen <br>
`self.roi_coords` = [y1, y2, x1, x2] list for the upper left and bottom right coordinates of the roi bounding box <br>
`self.roi_image` = (height, width, 3) array containing the region of interest image default None <br>
`self.full_screenshot` = (fullscreen width, fullscreen height, 3) array containing the fullscreen screenshot default None

`self.warn_enum` = enumerate containing the error messages as listed below <br>
`self.warn_ex_closed` = Boolean indicates whether the einscan software is closed, default true <br>
`self.warn_dist` = Boolean indicates whether the latest image contains the distance warning, default false <br>
`self.warn_signal` = Boolean indicates whether the latest image displays signal lost, default false not implemented yet <br>
`self.warn_track_lost` = Boolean indicates whether the latest image displays the tracking lost error, default false <br>
`self.warn_no_image` = boolean indicates whether the self.full_screenshot variable has an image or is empty default true

`self.dist_error_temp` = array containing the template used to match the distance error <br>
`self.track_lost_temp` = array containing the template used to match the tracking error

`self.image_h` = integer of the fullscreen screenshots height default 1080 <br>
`self.image_w` = integer of the fullscreen screenshots width default 1920

**Methods:** <br>
`self.einscan_status()`- <br>
Inputs = None <br>
Outputs = None <br>
Process = Checks to see if EXScan HX.exe is running on the PC, updates self.warn_ex_closed to false if the program is running and to true if it is not.

`self.temp_match(image, template)`-  <br>
Inputs = image array, template array <br>
Outputs = max matching value (float 0 - 0.99) <br>
Process = Uses opencv's template matching function to find a match in the image for a given template, the value returned is the best match >0.9 = positive match <0.9 = no match.

`self.get_roi()`-  <br>
Inputs = None <br>
Outputs = None <br>
Process = if self.warn_no_image is false finds the dimensions of the self.full_screenshot and compares to roi size to find bounding box of the roi, updates self.roi_image to an array containing just the area within the bounding box of self.full_screenshot

`self.warning_check()`- <br>
Inputs = None <br>
Outputs = None <br>
Process = Checks self.full_screenshot for distance and tracking lost errors using self.temp_match(). If >0.9 match is found on dist error then self.warn_dist is updated to true, if >0.9 is found on track lost error then self.warn_track_lost updated to true. If no matches meet the threshold then both errors are set to false.

`self.take_screenshot()`- <br>
Inputs = None <br>
Outputs = None <br>
Process = If the einscan software is open the function minimises all tabs except EXScan HX then takes a fullscreen screenshot which is then processed to the opencv format and set as self.full_screenshot. All tabs are then reopened. self.warn_no_image is set as false. If einscan is not open full_screenshot is set to None and warning 0 is printed.

`self.main()`- <br>
Inputs = None <br>
Outputs = None <br>
Process = potential workflow to retrieve and check screenshot. Runs einscan_status and take_screenshot. If an image is available it will run warning_check and if no warnings are found it will run get_roi. Otherwise roi_image is set to None.

`self.test_errors()`- <br>
Inputs = None <br>
Outputs = None <br>
Process = runs essentially the same process as main but sets full_screenshot to examples where they the errors are showing. Used to test code not useful for real program.