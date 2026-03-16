---
title: Our Journey
layout: default
nav_order: 5
---

# Our Journey

## Project Timeline

**S1Wk3 – Initial planning and requirement identification:** <br>
During this week we worked to identify the key objectives required to achieve the project, identify the requirements needed to achieve the objectives, and plan how the work would be progressed through the year. <br>
**S1Wk6 – System Architecture and Planning:** <br>
During this week we identified the overall system architecture and user scenarios to identify what sub-systems would be required. <br>
**S1Wk11 – Sub-System Targets:** <br>
The aim was to have each of the sub-systems designed and tested by this point. Progress was achieved in all sub-systems with only the dot detection being fully complete. <br>
**S2Wk1 – Start of Integration:** <br>
This was seen as the point at which integration between the systems should begin. At this point most of the code sub-programs had been finished with the only minor reformatting required to put them together in the main program. The production of a working mount was delayed leading to a bottleneck on the full system testing. <br>
**S2Wk6 – Current state:**<br>
Sub-systems are fully developed, integration has stalled due to technical challenges arising. With further development time progress could still be made, with know workarounds to these issues. <br>

## Project Development

The following link to documentation that shows the development of each individual sub-system through requirement identification, research, and testing.

* [Dot Detetction Development](./development/Dot%20Detection/dot_detection_development.md)
* [Screenshot and System Status Development](./development/Screenshot/get_screenshot_development.md)
* [Missing Region Coverage Algorithm Development](./development/Coverage_algorithm.md)
* [Robot Communications Development](./development/robot.md)
* [Trajectory Generation Development](./development/trajectory_generation.md)
* [User Interface Development](./development/UserInterface.md)


## Additional Challenges

**Robot availability:** <br>
Initially during this project we were assigned a UR5 robot arm, however, due to conflicting schedules we were reassigned. The new robot arm we were provided with was also later decommissioned, leading us to move to another UR10. This final robot arm provided unique difficulties due to its age. Overall this did not prove to be too much of an issue as the robot communication code was designed to work with any UR style robot platform. <br>
**Einscan HX Software:** <br>
The Einscan HX Software proved to be a unique difficulty during this project. The software is only available on Windows, meaning ROS could not be used to control the robot as it requires Linux for the specific tools needed. As such, a TCP client was used for the communications, however, this didn’t allow for the path planning features included within ROS making movements more uncertain. Additionally, the software does not include an API that can be used to send commands and receive data. This means that the graphical data of the part can only be gathered by taking screenshots of the GUI rather than point cloud data. This makes the missing region and trajectory generation solutions more complex. Furthermore, the software is very RAM-intensive due to the high graphics demand. Using the software on a local PC was denied and buying additional RAM or a high-end GPU would have fallen out of the budget. Therefore, the project had to go ahead with the small amount of processing power available from personal laptops. These issues provided unnecessary hurdles to meeting the objectives of the project and limited the scope due to additional complexity. <br>