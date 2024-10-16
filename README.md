# Payload Management Objectives
This project helps answer the common cFS question, “How should I design a cFS app to manage a science payload?” There are multiple approaches towards solving this problem. This project is not intended to provide a survey of designs, rather it shows a specific design that you can evaluate within the context of your situation.

A secondary objective is to show how a cFS library and application can be used to simulate a payload. This effective strategy lets you run your payload manager app prior to having a test configuration with the target hardware. It’s also a flexible environment that lets you test error paths.

[![cFS Payload Manager App Video](http://img.youtube.com/vi/RV-rATZ-Ad8/0.jpg)](https://www.youtube.com/watch?v=RV-rATZ-Ad8)

The slides used in the videos are contained in [Payload Manager Project Slides](https://github.com/cfs-apps/pl_mgr/blob/main/docs/plmgr-project.pdf)

## What you will need

This is a software-only project, so you only need cFS Basecamp running on a Linux desktop environment. You should be familiar with cFS Basecamp’s basic features that are described in the built-in tutorials.

## What you will learn
1. How to create and run a cFS target that includes the Payload Manager (PL_MGR) app, Payload Simulator ibrary (PL_SIM_LIB), and Payload Simulator (PL_SIM_LIB) app. l
2. How multiple apps and libraries can be designed to work together to achieve a goal.
3. How to simulate a hardware device using a library and app.

## Setup Steps
1. Install cFS Basecamp. You must build the default cFS target to generate the Python libraries before you run the Basecamp to perform step 2.
2. Download and add PL_MGR, PL_SIM, and PL_SIM_LIB.  PL_SIM_LIB must be added to the cFS target before the apps. This ensures when the cFS is started that the library will be loaded prior to the apps that depend on the library. Refer to Basecamp’s built-in “cFS GitHub App Exchange” tutorial for guidance.

## Operations
This ops video describes the payload detector simulation and how to use the PL_MGR and PL_SIM command and telemetry interfaces.  Perform the following exercises to learn the operational scenarios supported by the PL_MGR app.

1. Open the PL_SIM and PL_MGR telemetry pages

2. Nominal operations

Send PL_SIM Power On command
Send PL_MGR Start Science command
Send PL_MGR Stop Science command
Use File Browser to transfer science data files from flight to ground and use the text editor to open files and observe the simulated detector image data.

3. Anomalous operations:

Send PL_SIM Set Fault command
Send PL_MGR Start Science command
Send PL_MGR Reset Detector command
Send PL_MGR Stop Science command
Use File Browser to transfer science data files from flight to ground and use the text editor to open files. Observe how the science data was corrupted by the fault and that the detector reset restored the data.  The data may span multiple files, so you need to pay attention to the current science file name when you send the reset command.
Design 

This design video ops video describes the payload manager app design.

Code Exercises

Modify the DETECTOR_MON_CheckData() function in detector_mon.c to take corrective action when a fault is detected. The corrective action should be to reset the detector by calling PL_SIM_LIB_DetectorReset().  A solution is contained in DETECTOR_MON_Exercise1() at the end of the file. I encourage you to think of your own solution first before looking at this one. Note that in a real-world situation, you want a robust design that won’t react to a single instance of bad data.

Beyond Basecamp

There are a wide range of potential payload architectures, and this project is meant to help with the architectural design and show an example of creating science files.  Data collection is tightly coupled with the type of payload interface and with how the payload generates and stores data internally.  Using a 1Hz message from the scheduler to collect data wouldn’t apply to most real-world payloads, but it kept the design simple for this educational project. In general

Here are some things to consider for flight missions:

Ground software that processes payload data often needs auxiliary spacecraft state data, that corresponds with when the science data was collected.  There are multiple solutions for making this data available.  The payload manager app could subscribe to the auxiliary data and add write to separate files or combine it with the science data files.  Another app like NASA’s Data Storage app can be used to store selected auxiliary data from one or more telemetry packets and store them in a files.
Ground systems typically include a scripting language so the sequences described in  the “Operations” section can be automated. NASA has a Stored Command app that supports sending commands to other apps at either absolute times or at times relative to when the previous command was sent.
The PL_MGR design uses an internal app solution for detecting and correcting a fault and this  may be an adequate solution for spacecraft with few fault detection and recovery needs.  The cFS’ app-based architecture helps create solutions that can scale by using other apps such as NASA’s Limit Checker app to monitor for faults across the entire system.
