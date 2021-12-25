# The Arduino target files

The papyMonitor folder of papyMonitor-arduino repository contains two files to include on your project, see installation instruction.

## How it works

Each sketch must have some mandatory lines, here after is the minimal sketch to use together with the library

```cpp
// Mandatory include
#include "papyMonitor.hpp"

// Some variables to monitor
// ...
// ...
// ...

// The structure where you put your variables
// to monitor. The library uses this
sVariables_t variables[] = {
  // ...
  // ...
  // ...
};

/****************************************************************
 *****   Monitoring initialization, DO NOT MODIFY THIS !!!  *****
 ****************************************************************/
const uint8_t nbVariables = sizeof( variables ) / sizeof( sVariables_t );
Monitoring myMonitor( &Serial, variables, nbVariables );
/****************************************************************
 *****   End of Monitoring initialization                    *****
 ****************************************************************/
// Your setup
void setup() {
  // ...
  // ...
  // ...
}

// Your loop
void loop() {
  // ...
  // ...
  // ...

  // You need to add this line at the end of the loop
  // This line call the monitoring engine for the communication
  // with the papyMonitor-gui application
  // The call act as a blocking timer, after 1ms it unblocks the line
  // Further behaviour will be available if requested
  myMonitor.update();
}
```

Of course, this code is useless but shows what is absolutelly necessary in your sketch.

## Example

Assume you want to monitor two variables, one of the type *Byte* and one of the type *float*, the following sketch example shows how to do this:

```cpp
#include "papyMonitor.hpp"

// Some variables to monitor
// These are the declaration of the variables we want to monitor. They must be
// declared before the "sVariables_t variables[]"
float myFloat;  
uint8_t myByte;

// In order for the library to work, we need to tell it what variable
// to monitor, this is done in the sVariables_t variables[]
// In the sVariables_t variables[] we put one line per variable
// Each line contains 4 fields
// 1. The variable pointer (simply put a & in front of your variable)
// 2. The variable type (see eDataType_t in papyMonitor.hpp),
// 3. A flag that contains the reporting status (eNeverReport, eReportOff, eReportOn)
//    leave it at eReportOn for the moment
// 4. A Callback function, leave it at NULL for the moment
sVariables_t variables[] = {
  { &myFloat, eComDataType_Float, eReportOn, NULL },  //index 0 in the .lua file
  { &myByte, eComDataType_U8, eReportOn, NULL },      //index 1 in the .lua file
};
// The variables[] is in fact an array whose elements have an index. This is
// very important to note since each index is related with a peculiar variable
// and will be required in the .lua configuration file for the papyMonitor-gui

const uint8_t nbVariables = sizeof( variables ) / sizeof( sVariables_t );
Monitoring myMonitor( &Serial, variables, nbVariables );


void setup() {
  // Initialization of the variables
  myFloat = 0.0f;
  myByte = 0;
}

void loop() {

    // Main program

    // We increment the myFloat value from 0 to 100 forever
    // in step of 0.01
    myFloat += 0.01f;
    if (myFloat >= 100.0f)
      myFloat = 0.0f;

    // We increment the myByte value from 0 to 255 forever
    myByte++;

    myMonitor.update(); // unblock every 1ms
}
```

The matching .lua configuration file can be:

```Lua
-- Lua configuration file
-- See "The User Configuration File (UCF)"" documentation

cfg:Default({  
    Baudrate = 230400,
    SetValue = '#',
    ReportValueOn = '?',
    ReportValueOff = '!',
    IReportValue =        ':',
    IReportTextConsole =  '>',
    SampleTimeHW= 0.001,
    GroupTabs= { 
        {-- group0
            Tabs = {
                { -- Tab0
                    TabName="Example",
                    Columns= {
                        { 0, },
                        { 1 },
                    }
                },
            },
        },
    },
    Plot=true,
})

---------------
cfg:Variable({
    Name="My float",
    Type= "f",
    Index= 0,
    Data = {
        {
            CanPlot=true,
        }
    }
})

---------------
cfg:Variable({
    Name="My byte",
    Type= "B",
    Index= 1,
    Data = {
        {
            CanPlot=true,
        }
    }   
})
```

See "The User Configuration File (UCF)"" documentation for more information
