# Welcome to papyMonitor

papyMonitor-gui is a multiplatform (Windows, Linux, Mac, ...) tool to interactively receive/edit/monitor data and send commands to embedded system with the serial port. The embedded system can be Arduino (all flavors supported), PIC, AVR, ARM, RPI,... or a computer.

The tool provide a multifunction plotter as well a 3D display to simulate real mechanical systems. It is fully customizable with one User Configuration File (UCF) based on Lua scripting language.

It is fast, real time (the embedded system is the master) and proudly made with Godot Engine. The development language used is C#

This tool is already used in production but -for sure- still has some bugs to discover. The MAC version has not been tested since we don't have a MAC, so developers are welcome.

## How it works?

* `mkdocs new [dir-name]` - sss Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.
