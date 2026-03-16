---
title: Remote Controlling the UR5e
layout: default
parent: Development Overview
has_toc: true
---

# Remote controlling Universal Robots industrial robot arms

Universal Robots produces a series of robot arms of different product generations and in different sizes. These arms are able to be controlled both internally using Universal Robot's own tools and programming interface on the Teach Pendant, and externally through various protocols and interfaces provided by Universal Robots and by third parties. As our solution requires externally controlling the robot in order to coordinate its motion with the information calculated from the scanner we considered and attempted the use of several of these, detailed below.

## ROS/ROS2

The Robotics Operating System (ROS/ROS2) is one of the most popular ways to externally control Univesal Robots. It is a open-source framework for middleware to interface between different robot controllers, sensors, industrial machines and computers[^1]. Due to its popularity and support provided by the Universal Robots team[^2] there is a very mature ecosystem for controlling Universal Robots industrial robot arms through ROS, including I/O control, obstacle-avoidant path planning, real-time feedback and other features.

The main drawback of the UR ROS/ROS2 driver however is that it is only compatible with specific distributions of GNU/Linux. While these can be containerised fairly well, the other software that is required for this project cannot be run in Linux or easily containerised as it is not intended to be compatible with virtualisation and requires both specific hardware acceleration and I/O access, and is also legacy software with no available English language support.

As a result while ROS could be used to control the robot, this would not solve the connectivity issue between the robot and the software running on the scanner computer, as another communication link would need to be established either between two separate machines or between a containerised ROS and the host machine. Due to the high complexity of such a solution and the lack of software engineering experience within the team this option was subsequently discarded.

## Industrial Protocols

UR series robots can also be connected to a variety of industrial protocols, including MODBUS TCP[^3], Siemens Profinet[^4] and OPC UA[^5]. These protocols are highly robust, built for industrial settings and easy to implement due to their existing integration with the robot. However these solutions are not intercompatible, and the choice of any one of them would lock our solution into a specific vendor that would occur significant cost for any client not already using that vendor, which is not an acceptable limitation for our solution.

## Client Protocols

The robots also supports a number of independent protocols[^6] such as the Dashboard Server, RTDE and others. These can be accessed directly using socket communication via the TCP protocol, allowing for the greatest flexibility of implementation. However they also are the least supported by third-party libraries and solutions, and as such should only be used when other solutions are not appropriate for the problem at hand.

## Bibliography
[^1]: Open Robotics, “ROS.org \| Powering the world’s robots,” Ros.org, 2020. https://www.ros.org/ \[Accessed: 17-Feb-2025\]
[^2]: “UniversalRobots/Universal_Robots_ROS_Driver,” GitHub, Jun. 08, 2021. https://github.com/UniversalRobots/Universal_Robots_ROS_Driver \[Accessed: 17-Feb-2025\]
[^3] “Modbus Server - 16377 \| Universal Robots Support,” Universal-robots.com, 2024. https://www.universal-robots.com/articles/ur/interface-communication/modbus-server/ \[Accessed: 17-Feb-2025\]
[^4] “Profinet Guide - 20596,” Universal-robots.com, 2025. https://www.universal-robots.com/articles/ur/interface-communication/profinet-how-to-guide-e-series/ \[Accessed: 17-Feb-2025\]
[^5] “OPC UA Client/Server,” Universal-robots.com, 2025. https://www.universal-robots.com/marketplace/products/01tP40000071NgaIAE/ \[Accessed: 17-Feb-2025\]
[^6] “Overview of client interfaces - 21744,” www.universal-robots.com. https://www.universal-robots.com/articles/ur/interface-communication/overview-of-client-interfaces/ \[Accessed: 17-Feb-2025\]