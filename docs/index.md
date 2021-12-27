# Welcome to papyMonitor

![main.jpg](main.gif)

***papyMonitor*** (called after *host*) is a multiplatform (Windows, Linux, Mac) tool to interact with any embedded system (called after *target*). It can receive/edit/monitor/simulate data and send commands to the target via the serial port. Arduino (all flavors supported), PIC, AVR, ARM, RPI, a computer or whatever equiped with a serial port is supported.

The tool provides a multifunction plotter, a 3D view to simulate the target attached mechanical systems and a way to load/save variables tagged as parameters. The GUI and the behavior are fully customizable with one User Configuration File (called after *UCF*) based on Lua scripting language.

It is fast, real time (the embedded system is the master) and proudly made with Godot Engine.

## How it works?

The user write the UCF that describe the application behavior (what to do with the variables, editing, displaying, ploting, linking with a move on a 3D shape,...).

On the target side, a provided small C library (CPP for Arduino) gives the necessary functions for the communication as well as a structure to store the variables (and callbacks if any) to be reported/modified.

The protocol used over the serial port is choosen as plain ASCII text. Each line is terminated with a `'\n'`. It is very simple, the variables are sent one at a time from the target to host at a regular interval, this interval is choosen by the target. Depending on the variable type, a line has at max 13 chars included the `'\n'`. This protocol was sucessfully tested at 10000 samples/per second at 2MBaud on a Cortex-M4. The code on Godot Engine takes the received lines asynchronously, at its  frame rate, and process them.

## Documentation

- **The User Configuration File (UCF)** describes the format and the structure of the UCF
- **Installation and usage** provide informations for installing and using the tool on platforms
- **papyMonitor GUI** describes the application
- **The Arduino target files** describes the Arduino library and how to use it
- **The generic C target files** describes the general C library and how to use it
- **The GUI application** explains how to use the GUI application
