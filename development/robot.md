---
title: Remote Controlling the UR5e
layout: default
parent: Development Overview
---

> **Important:** The robot listed on this page is the robot we expect this system to be using in production. Testing will be done on various UR robots, which can have minor differences to the final product.
{: .important }

# Universal Robots UR5e Collaborative Industrial Robot Arm

The Universal Robots UR5e is a collaborative industrial robot arm. Collaborative robots, often shorted to cobots are robots which are designed to be able to perform tasks in collaboration with workers, having a number of technical features such as lightweight materials, padding and embedded sensors to reduce the risk of harm to humans.[^1]

## Technical Specifications

The technical specifications of the UR5e can be found in its user manual[^2], these cover almost all factors relevant to the current application, but Universal Robot makes no statements about the pose accuracy of their robots in official material, only the pose repeatability (± 0.03 mm). The pose accuracy of UR5 series robots has nevertheless been measured by independent researchers. The older UR5 robot only lists a pose repeatability of ± 0.1 mm[^3], and has been measured to have a pose accuracy within the same range[^4] as such it can be safely assumed the updated UR5e model has a pose accuracy at least as good as the older UR5, of ± 0.1 mm.

Knowing the pose accuracy of the robot is essential for this task as in order to perform a dynamic laser scan the program must be able to move the robot to a new position accurately, and must know the position from which a current scan is taken accurately in order to evaluate it. If the robot's internal position data could not be trusted to a high enough level of accuracy an external position measurement tool would need to be added to the system.

# UR5e Connection Interfaces
As our team is working on an automation solution, one of the most important things we must consider about the robot is how to interface it with our other subsystems. The UR5e can be interfaced with in numerous ways, which are listed and evaluated below.

## Control Box I/O
The UR5e's control box has a panel of I/O ports[^2] intended for direct wiring. These are mainly intended for connecting the robot to external industrial and safety equipment like emergency stop circuits and programmable logic controllers. They are not appropriate for connection to a microcontroller as in our use case as they run at 24V whether internally or externally powered, meaning they cannot be driven by the vast majority of commercial microcontrollers that run at 5V or 3.3V without excessive use of relays for every single pin, and because of the low bandwidth of the connection as each port is only a binary digital input/output without reliable means of establishing a serial connection.
![Local image](./media/UR5e_IO.png)

## 8P8C Port (Ethernet)

The UR5e also has an 8P8C port in the bottom of the control box[^2] used for serial communication. This connection also supports some industrial protocols like MODBUS TCP[^5], Siemnes Profinet[^6] and OPC UA[^7].

### Industrial Protocols

While the robot supports multiple different industrial protocols such as Modbus TCP, Siemens Profinet and OPC UA, the way these interface with the robot is fundamentally similar. The protocols can each read and write values to a set of registers that are shared between the industrial controller and the robot's internal controller. The robot's internal programming then determines how the system responds to the values set in these registers. As such none of these systems can fully remote control the robot as the robot must first be started manually in order to enable these registers. Further the robot has a native interface called RTDE that has access to a similar set of registers, which can be used from any microcontroller that can open a socket, unlike industrial protocols that may require proprietary hardware like with Profinet, or not be included natively with the UR5E and need to be purchased separately to be supported[^7]. While in an eventual industrial solution the use of one of these protocols may be preferred due to their security, reliability, and existing infrastructure in factories that may already run other systems over Modbus or another protocol, for our solution the client interfaces offer a cheaper and more flexible option that is easier and faster to implement.

### Client Interfaces

The robot also exposes a series of interfaces that can be connected to directly using server sockets. These are the primary and secondary interfaces as well as the real time data exchange (RTDE) interface which replaces the older deprecated real-time interface, all of which handle data exchange via registers. The robot can also be controlled through the dashboard server socket, sent commands through the URScript interpreter socket and can interface with external functions and methods through an XML-RPC socket[^8]. All of these can be used in parallel with each other, as each has different functions. For a complete remote control system the Dashboard Server socket must be used as this is the only interface by which the robot's power and safety state can be managed as well as internal programs loaded and started [^9]. From there, different sockets present good options depending on how program control flows between different subsystems. If a solution is desired in which the robot has zero part in the control flow, the Interpreter Mode socket[^10] can be used to control the robot without needing to program anything on the robot beyond a dummy program that sets the robot into "Interpreter Mode". From there all commands are passed to the robot directly through this socket. In the reverse, to maximise control flow on the robot the XML-RPC socket can be used, where instead the robot can remotely call functions from an external program[^11] fully from within an internal control flow. The primary, secondary and real-time/RTDE interfaces meanwhile are used for data exchange and would involve a shared control flow between the robot and a program running on the other end of the Ethernet connection[^12].

## ROS/ROS2

The Robotics Operating System (ROS/ROS2) is one of the most popular ways to externally control Univesal Robots. It is a open-source framework for middleware to interface between different robot controllers, sensors, industrial machines and computers[^1]. Due to its popularity and support provided by the Universal Robots team[^2] there is a very mature ecosystem for controlling Universal Robots industrial robot arms through ROS, including I/O control, obstacle-avoidant path planning, real-time feedback and other features.

The main drawback of the UR ROS/ROS2 driver however is that it is only compatible with specific distributions of GNU/Linux. While these can be containerised fairly well, the other software that is required for this project cannot be run in Linux or easily containerised as it is not intended to be compatible with virtualisation and requires both specific hardware acceleration and I/O access, and is also legacy software with no available English language support.

As a result while ROS could be used to control the robot, this would not solve the connectivity issue between the robot and the software running on the scanner computer, as another communication link would need to be established either between two separate machines or between a containerised ROS and the host machine. Due to the high complexity of such a solution and the lack of software engineering experience within the team this option was subsequently discarded.

# What have we used?

We have decided to use the client interfaces as these can be accessed using a TCP client/server architecture, which is implemented in every major operating systems and as such places no limitations on our hardware or software, while still allowing us to access the robot's full functions. This does place a greater development workload on our team however as these interfaces are not as well supported by third-party libraries outside of their integration into the ROS/ROS2 driver.

### Dashboard Server

The most essential part of any automation of a UR series robot is the Dashboard Server[^9]. This is a TCP server, and is the only way to interact with the robot's safety and control systems that start and stop the robot's program. Without this system, no full automation solution is possible, as the robot would still need to be powered on and started by a technician every time the system is run. In our solution the Dashboard server is integrated so that the entire automation solution can be started from our graphical user interface.

### Robot TCP Client

Our main other system involves using the robot as a client to connect to our system and pull data from it to control robot movements. This solution is very flexible and does not require any specific software or hardware to be available on the computer system used, unlike the more popular RTDE solution which requires real-time scheduling on the kernel for some functions.

## Bibliography
[^1]: International Federation of Robotics (IFR), Demystifying Collaborative Industrial Robots – Positioning Paper, updated December 2020. Available: https://www.automate-uk.com/media/4jmhne5p/ifrdemystifyingcollaborativerobotsupdatev03dec2020.pdf \[Accessed: 03-Nov-2025\]
[^2]: Universal Robots A/S, UR5e – User Manual (Original Instructions), e-Series, 2009–2024. [Online]. Available: https://www.universal-robots.com/manuals/EN/PDF/SW5_19/user-manual-UR5e-PDF_online/710-965-00_UR5e_User_Manual_en_Global.pdf \[Accessed: 03-Nov-2025\]
[^3]: Universal Robots A/S, UR5/CB3 – User Manual (Original Instructions), CB-Series, 2009–2021. [Online]. Available: https://s3-eu-west-1.amazonaws.com/ur-support-site/105370/99202_UR5_User_Manual_en_Global.pdf \[Accessed: 26-Dec-2025\]
[^4]: M. Pollák, M. Kočiško, D. Paulišin, and P. Baron, “Measurement of unidirectional pose accuracy and repeatability of the collaborative robot UR5,” Advances in Mechanical Engineering, vol. 12, no. 12, p. 168781402097289, Dec. 2020, doi: https://doi.org/10.1177/1687814020972893.
[^5]: “Modbus Server,” Universal Robots, Dec. 15, 2025. https://www.universal-robots.com/articles/ur/interface-communication/modbus-server/ \[Accessed: 30-Dec-2025\]
[^6]: “Profinet Guide - 20596,” Universal-robots.com, 2025. https://www.universal-robots.com/articles/ur/interface-communication/profinet-how-to-guide-e-series/ \[Accessed: 30-Dec-2025\]
[^7]: “OPC UA Client/Server,” Universal-robots.com, 2025. https://www.universal-robots.com/marketplace/products/01tP40000071NgaIAE/ \[Accessed: 30-Dec-2025\]
[^8]: “Overview of client interfaces - 21744,” www.universal-robots.com. https://www.universal-robots.com/articles/ur/interface-communication/overview-of-client-interfaces/ \[Accessed: 30-Dec-2025\]
[^9]: “Dashboard Server e-Series, port 29999 - 42728,” Universal-robots.com, 2025. https://www.universal-robots.com/articles/ur/dashboard-server-e-series-port-29999/ \[Accessed: 30-Dec-2025\]
[^10]: "UR Script Interpreter mode", Universal-robots.com, 2025. https://www.universal-robots.com/articles/ur/programming/interpreter-mode/ \[Accessed: 30-Dec-2025\]
[^11]: “XML-RPC communication - 16326,” www.universal-robots.com. https://www.universal-robots.com/articles/ur/interface-communication/xml-rpc-communication/ \[Accessed: 30-Dec-2025\]
[^12]: “Remote Control Via TCP/IP - 16496,” www.universal-robots.com. https://www.universal-robots.com/articles/ur/interface-communication/remote-control-via-tcpip/ \[Accessed: 30-Dec-2025\]
[^13]: Open Robotics, “ROS.org \| Powering the world’s robots,” Ros.org, 2020. https://www.ros.org/ \[Accessed: 17-Feb-2025\]
[^14]: “UniversalRobots/Universal_Robots_ROS_Driver,” GitHub, Jun. 08, 2021. https://github.com/UniversalRobots/Universal_Robots_ROS_Driver \[Accessed: 17-Feb-2025\]