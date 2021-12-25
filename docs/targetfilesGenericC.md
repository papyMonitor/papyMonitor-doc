# The generic C target files

The papyMonitor-microcontroller-generic repository contains two files to include on your project, see installation instruction.

## How it works

The files **monitoring.c** and **monitoring.h** are the mandatory files to add to your embedded project. These files must not be modified until you know what you are doing.

Let's take a look to **monitoring.h**:

```c
#ifndef MONITORING_H_
#define MONITORING_H_

#include "stdint.h"
#include "stdbool.h"

// Meaning codes
typedef enum {
    // Commands from monitor to target;
    eSetValue = '#',
    eReportValueOn = '?',
    eReportValueOff = '!',

    // Messages from target to monitor
    eIReportValue =        ':',
    eIReportTextConsole =  '>',
} eHostMonitoringCmd_t;

typedef enum {
    eComDataType_Float  = 'f', // 8 chars
    eComDataType_U32    = 'W', // 8 chars
    eComDataType_I32    = 'w', // 8 chars
    eComDataType_U16    = 'I', // 4 chars
    eComDataType_I16    = 'i', // 4 chars
    eComDataType_U8     = 'B', // 2 chars
    eComDataType_I8     = 'b', // 2 chars
} eDataType_t;

typedef enum {
    eNeverReport,
    eReportOn,
    eReportOff,
} eReportType_t;

typedef struct {
    void* data; // Pointer to the data
    eDataType_t type;
    eReportType_t report;
    bool (*cb)(uint8_t); // Callback with index of the variable in the table as argument
} sVariables_t;


// Optional helper functions
bool setVariablesReportingAllOn( uint8_t dummy );
bool setVariablesReportingAllOff( uint8_t dummy );

/*
 * Must be called Every X microsecond
 * Report variables
 */
void update( void );

#endif /* MONITORING_H_ */
```

There are enum definitions that are used by **monitoring.c** file and the **sVariables_t** structure. The **sVariables_t** structure is defined in your project and contains the variables to report/edit.
The **update** function is a function that you have to call every X (micro or milli) seconds for the variables to be reported. One value is reported at a time. This function checks also for command arrival and execute it.

Let's take a look to the beginning of **monitoring.c**:

```c
#include "stdio.h"
#include "stdbool.h"
#include "stdint.h"
#include "string.h"
#include "math.h"

#include "monitoring.h"

extern char msgFromHost[];  // defined in uart.c
extern bool messagePending; // defined in uart.c
extern void sendDataHost( char* message ); // defined in uart.c

extern sVariables_t variables[];
extern const uint8_t nbVariables;
```

There are 5 extern objects that you must define in your project:

- **msgFromHost[]** is a buffer that you must define and initialise with a length of 13 char (minimum). This buffer must contain any message from the host, you have to write the code for the serial receiving. On your code, as soon as a string terminates with a '\n', copy it to **msgFromHost** and set the flag **messagePending** to true. See the code example hereafter.
- **messagePending** is a boolean that you must define and initialize to *false*. When your serial receiving code has set the **msgFromHost** buffer, it must also set **messagePending** to true to tell **monitoring.c** that a new command is arrived. **monitoring.c** take care to reset **messagePending** to false.
- **sendDataHost(char*message)** is a function that you must define. This function has to send over the serial port the buffer message. The buffer message length is 14 chars maximum and **ALWAYS** terminate with '\0' (it's a string).
- **variables[]** is an array of objects in which you put your variables to report. See the code example hereafter.
- **nbVariables** is the number of elements in **variables[]**, you must define this. See the code example hereafter.

## Code example

```c
/*
 * THIS IS A BASIC FILE JUST TO SHOW HOW TO
 * DEFINE AND INITIALIZE THE VARIABLES NEEDED BY monitoring.c
 * THIS FILE IS NOT OPTIMIZED AT ALL
 */

#include "monitoring.h"

#include "string.h"
#include "structures.h"

// Variables the project is using
float myFloat1, myFloat2;

// In order for the library to work, we need to tell it what variable
// to monitor, this is done in the sVariables_t variables[]
// In the sVariables_t variables[] we put one line per variable
// Each line contains 4 fields
// 1. The variable pointer
// 2. The variable type (see eDataType_t in papyMonitor.hpp),
// 3. A flag that contains the reporting status (eNeverReport, eReportOff, eReportOn)
//    leave it at eReportOn for the moment
// 4. A Callback function, leave it at NULL for the moment
sVariables_t variables[] = {

    { (void *) &myFloat1,   eComDataType_Float, eReportOn, NULL }, // Index 0
    { (void *) &myFloat2,   eComDataType_Float, eReportOn, NULL }, // Index 1
};
// Create and init the nbVariables var
const uint8_t nbVariables = sizeof( variables ) / sizeof( sVariables_t );

// Create the msgFromHost[] var
char msgFromHost[14];

// Create the messagePending flag
bool messagePending = false;

/* ****************************************************************************
 * Create your serial receive handler
 * used when the host send a command
 * This handler set msgFromHost[] and messagePending
 ****************************************************************************/
// This is just an example
void UART0_IRQHandler( void ) {
    static uint8_t idxRX = 0;
    static uint8_t localBuffer[14]; // Local buffer for double buffering

    // A byte is arrived, store it to a local buffer
    char c = Chip_UART_ReadByte( LPC_USART0 );

    localBuffer[idxRX] = c;

    if ( (c == '\r') || (c == '\n') ) {
        // Copy to msgFromHost for monitoring.c
        memcpy( msgFromHost, interruptMezza2Stab.msgString, idxRX);
        // Say to monitoring.c that a message is pending
        messagePending = true;
        // Reset reception
        idxRX = 0;
    } else {
        idxRX++;
        // Overflow protection
        if ( idxRX == 13)
            idxRX = 0;
    }
}

// Create the sendDataHost function
void sendDataHost( char* msgString )
{
    uint8_t i=0;
    while(msgString[i]!=0) {
        //Your function for serial sending char
        sendChar(msgString[i]);
    }
}

/* ****************************************************************************
 *  Your main function
 ****************************************************************************/
int main( void ) {

    while(1)
    {
        // Do your stuff
        myFloat1 = ...
        muFloat2 = getADCValue(...);

        // Call the update function 
        // each X (micro-milli) second
        if (YourTimerReachXSecond)
            update();
    }

    return 0;
}
```

For amore complete example, see the example02 in the examples folder
