---
title: User Interface
layout: default
parent: Development Overview
---

# Remote Scanner Supervision Subsystem

## Web-Based User Interface

The web-based user interface was built using the gradio library, a python library with in-built functions for the simple creation of a public url, allowing the system to be remotely supervised by the operator, which would improve the efficiency of the operator when a large scan is being conducted; the lightweight, web-based functionality afforded to the system by the gradio infrastructure means the process can be supervised remotely from any device. The real-time status updates, and the clear and specific error messages allow the operator to receive the same immediate feedback as they would if they were standing next to the scanner. This subsystem revolutionises what used to be an arduous manual time consuming process, turning it into a low effort background task that can be supervised remotely between other responsibilities, significantly improving overall laboratory productivity.

## Testing & Validation

### Unit Testing

Unit testing during the development was done to ensure individual functionality has been accomplished, for example, confirming the pause_scan() method responds accordingly and correctly updates the UI. The subsystem was also tested using a programmed simulated error, ensuring the UI correctly displays error messages and the system only moves between valid states, so the system cannot start a scan in error state, a very critical part of automated robot scanning, as invalid operational sequences can cause hardware damage or health hazards to those in the vicinity.

### User Validation

The subsystem was validated through the usage of two non-technical volunteers, who attempted to successfully complete all of the subsystems core tasks: starting, pausing, resuming and terminating scans, without any prior instruction or discussion of the UI functionality, validating the intuitive design of the subsystem. The feedback from the users was conclusive, buttons were successfully attributed to their corresponding system functionality, indicating that the interface can safely be operated by users with minimal subsystem training. The only confusion identified was with the “Clear error” button, suggesting that a more prescriptive label, or the usage of tooltips, may be required.