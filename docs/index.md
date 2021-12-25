# Welcome to papyMonitor

![main.jpg](main.gif)

***papyMonitor*** is a multiplatform (Windows, Linux, Mac, ...) tool to interact with any embedded system (called after *target*). It can receive/edit/monitor/simulate data and send commands to the target via the serial port. Arduino (all flavors supported), PIC, AVR, ARM, RPI, a computer or whatever equiped with a serial port is supported.

The tool provide a multifunction plotter, a 3D display to simulate the attached mechanical systems and a way to load/save variables tagged as parameters. The GUI and the behavior are fully customizable with one User Configuration File (called after *UCF*) based on Lua scripting language.

It is fast, real time (the embedded system is the master) and proudly made with Godot Engine.

## How it works?

The user write the UCF to describe what variables is received from the target and how to deal with. The behavior (what to do with the variables, editing, displaying, ploting, linking with a move on a 3D shape,...) is also described.

On the target side, a provided small C library (CPP for Arduino) gives the necessary functions for the communication as well as a structure to store the variables (and callbacks if any) to be reported/modified.

The protocol used over serial port is choosen as plain ASCII text. Each line is terminated with a `'\n'`. It is very simple, the variables are sent one at a time from the target to ***papyMonitor*** (called after *host*) at a regular interval, this interval is choosen by the target. Depending on the variable type, a line has at max 13 chars included the `'\n'`. This protocol was sucessfully tested at 10000 samples/per second at 2MBaud on a Cortex-M4. The code on Godot Engine takes the received lines asynchronously, at the Frame Rate, and process them. Our tests never show a Frame Rate slow down (standard of 75FPS), even with some Lua functions on the UCF and several 3D shapes on the 3D view.

## Documentation

- **papyMonitor GUI** describes the application
- **User Configuration File (UCF)** describes the format and the structure of the UCF
- **Target files** describes the library and how to use it
