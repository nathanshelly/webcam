# Webcam

*Built by Jacob Bruce and Nathan Shelly*

## Description

This project aims to walk through the steps of designing and building a webcam from scratch. We'll walk through PCB (printed circuit board) design, soldering of components, 3D printing of the outer enclosure, MCU code and website code to enable you to make your own webcam, or modify our design however you'd like! This repository acts as the landng page to various parts of the project. Here we'll have various files such as our PCB designs, our CAD files and a bill of materials. We'll link to two different code repositories, one for our MCU code and one for our website code.




1. Order all components

2. Configure server

3. Solder components onto board

If you have experience with surface mount soldering, this should be fairly straightforward. If you do not, we recommend practicing some before working with any expensive components. Our instructor's videos at <link> provide a great tutorial.

We recommend the following soldering order to unit test each component before moving forward.

    i. Power circuit: barrel jack, voltage regulator, power indicator LED, associated resistors and capacitors. Confirm that ground and power are not connected via continuity test. Plug in power cord and confirm that the power indicator lights up.
    ii. Microcontroller, PLL circuit, crystal oscillator, buttons, associated bypass capacitors, and header pins. After soldering the MCU, confirm each connection via continuity test to another via or pad, and confirm that no pins are connected to power or ground that shouldn't be. Connect the Atmel debugger, power the circuit, and confirm that the device signature can be read from Atmel Studio.
    iii. Wifi Chip, indicator LEDs, and associated bypass capacitors. Again, confirm each connection individually before proceeding. To confirm proper operation, perform step 4 to configure the chip.
    iv. Microphone and associated passive components.
    v. Camera module and associated passive components. Be sure to solder the connector without the module inserted, and again confirm connections individually before proceeding.

4. Configure wifi chip

5. Configure microcontroller

6. Print enclosure

7. Enjoy!
