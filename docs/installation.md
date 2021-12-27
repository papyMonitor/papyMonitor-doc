# Installation and usage

To install the application, go to [Releases](https://github.com/papyMonitor/papyMonitor-gui/releases) and select/download the file that suits your platform.

Save the zipped file in a folder of your choice. The executable on Windows is **papyMonitor.x86_64.exe**, on Linux is **papyMonitor.x86_64** and on MAC is TBD (we don't know)

## Target Arduino

The **papyMonitor** folder of **papyMonitor-arduino** repository is a library that you **must** put on your sketchbook location. To see where your sketchbook location is, open the Arduino IDE then select Files->Preferences: A popup windows will show the "Settings" tab, the location is under the "Sketchbook location".
Open this location on your disk and you'll find a folder "libraries". Copy the folder "papyMonitor" inside the folder "libraries". That's it!

### Usage/Documentation

For each sketch you make (.ino file) you must write a matching .lua file for the papyMonitor application to understand what is coming from the serial port. See the **The Arduino target files** documentation and see the examples in the folder examples of the **papyMonitor-arduino** repository.

## Target other (Generic C target files)

Simply copy the two files (**monitoring.h** and **monitoring.c**) of the **papyMonitor-microcontroller-generic** repository to your project folder and reference them for compilation.

### Usage/Documentation

For each firmware you make, you must write a matching .lua file for the papyMonitor application to understand what is coming from the serial port. See the **The generic C target files** documentation and see the examples in the folder examples of **papyMonitor-microcontroller-generic** repository.
