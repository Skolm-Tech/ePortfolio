---
title: Our Vision
layout: default
nav_order: 3
---

# Our Vision

## Our Aim 

To develop an automated system for assessing the completeness of laser scan data from existing handheld scanners through image analysis methods which can dynamically generate scanner trajectories to improve scan completeness in real time. 

## Our Objectives 

To investigate the problem domain through researching existing solutions in laser scanning technologies, scanning automation methods and industrial robot control; and through identifying a set of stakeholders and establishing their requirements. 

To define system requirements from the established stakeholder needs and produce a high-level system design including appropriate system diagrams and interface definitions 

To critically evaluate existing and novel technical solutions to design the subsystems found in the high-level system design through relevant academic literature and industrial sources. 

To design and program a digital interface to the provided laser scanner enabling control of scanner operations and extraction of scan data in a form useful to other subsystems. 

To design and test an algorithm to analyse extracted laser scan data for completeness and quality, and to infer spatial and positional information where available. 

To design and test an algorithm to generate robot trajectories and new scanner positions based on areas of scan incompleteness. 

To implement a communication protocol for interfacing with the industrial robot, enabling reliable control of robot operations and motion paths. 

To design and fabricate a mechanical interface to connect the laser scanner to the robot tool flange without interfering with scanner operations. 

To design a user interface that enables intuitive interaction with the system for configuration and monitoring of scanning processes. 

To manage our team effectively by defining roles and responsibilities, actively maintaining shared documentation resources with version control methodologies and conducting regular meetings to review team performance and address issues. 

To produce presentations and a project portfolio to communicate our project goals and progress to both technical and non-technical audiences. 

To maintain individual logbooks to record contributions and personal and professional development. 

# Stakeholder Analysis 

## What is a stakeholder? 

Stakeholder: An individual, group of people, organisation or other entity that has a direct or indirect interest (or stake) in a system. A stakeholder’s interest in a system may arise from using the system, benefiting from the system (in terms of revenue or other advantage), being disadvantaged by the system (in terms, for instance, of cost or potential harm), being responsible for the system, or otherwise being affected by it.[^1]

## Who are our stakeholders? 

Developers: Our system is mainly a software solution, which will involve a large development process to ensure functionality, reliability, efficiency and easy of use. Our developers require the system to be well-specified, with clarity on where the priorities are and how the system will be maintained after initial development is over. They are also interested in a system with realistic development deadlines and demands. 

Team Leads: The development managers will mostly be interested in having clear non-functional requirements for the system that are relevant to software development, such as expected perfomance and scalability, as well interfaces for future expansion, as they will need to consider these when construction the high-level design of the software. 

Testers: During development our system will also need to be tested reqularly to ensure system requirements as well as industry and regulatory standards are being met. Testers are interested in well-defined requirements that are easily measureable and testable, as well as a development timetable that takes into account sufficient time both to perform tests and to have developers respond to the results of those tests. 

Operators: The operators of the existing systems will be interested in how easily they can work with the new system and what training will be required. They also have concerns around job security due to the automation of their previous task, as well as a loss of control, as QA personnel they might not trust the system to be working properly if they can't review it in the way they would the manual process. 

System Maintainers/Technicians: The maintainers of the existing system will be interested in a new system that is designed to be easy to maintain and to reduce downtime. They will want a system that has good documentation and diagnostics tools, and which is designed to be repairable on-site by an existing technician. 

Factory Owners: The owners of factories and managers of firms that will be using the system will be interested in the system providing a good return on their investment by increasing throughput or reducing costs. They will also be concerned about the rollout of the new system and want something they can integrate into their existing process with minimal effort. Furthermore they will want a system that is ccertified and compliant with regulations and industry standards. 

Regulatory Authorities: Regulatory authorities will demand that the system is compliant with legal standards and that this compliance is certified by relevant bodies. Especially for critical systems like aerospace components that this system will be designed to work with, accountability for and traceability of QA decisions will be crucial to regulatory authorities. 

Labour Unions: As our system is an automation of an existing process, labour unions will be concerned about how the system will affect existing workers. Do they risk being laid off, does the new system introduce any safety risks, will it lead to unrealistic workloads for remaining staff to meet increased productivity demands? They will want to be consulted throughout the design and deployment process to be able to impact decisions and policies around the system. 

System Disposers: Our system is intended as a replacement of an existing system, and as such brings up numerous sustainability and environmental concerns. System disposers will want the existing system to be reused where possible to reduce waste, and will want the new system to be designed to reduce its environmental impact throughout its entire lifecycle. 

## Stakeholder Categorisation

<table>
<tbody>
  <tr>
    <td colspan="2" rowspan="2"></td>
    <td colspan="2">Interest</td>
  </tr>
  <tr>
    <td>High</td>
    <td>Low</td>
  </tr>
  <tr>
    <td rowspan="2">Power</td>
    <td>High</td>
    <td>Team Leads<br>Factory Owners<br>Labour Unions</td>
    <td>Regulatory Authorities</td>
  </tr>
  <tr>
    <td>Low</td>
    <td>Operators<br>System Maintainers<br>Developers</td>
    <td>Testers<br>System Disposers</td>
  </tr>
</tbody>
</table>

# Diagram of generic solution

![Local image](./media/HLD_Skolm.png)

## Bibliography
[^1]: J. Dick, E. Hull, and K. Jackson, Requirements Engineering. Cham Springer, 2017.