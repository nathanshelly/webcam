# Webcam

*Built by Jacob Bruce and Nathan Shelly*

## Description

This project aims to walk through the steps of designing and building a webcam from scratch. We'll walk through PCB (printed circuit board) design, soldering of components, 3D printing of the outer enclosure, MCU code and website code to enable you to make your own webcam, or modify our design however you'd like! This repository acts as the landng page to various parts of the project. Here we'll have various files such as our PCB designs, our CAD files and a bill of materials. Our [microcontroller code](https://github.com/nathanshelly/webcam_mcu_code) and [server code](https://github.com/nathanshelly/webcam_web_code) each have their own repositories, where our full code for each can be explored or downloaded. If you have any interest in duplicating or extending the project, please contact the authors - we'd be happy to help out in any way we can.

3D Printed Enclosure             |  Wood Milled Enclosure
:-------------------------:|:-------------------------:
![plastic](./images/plastic.jpg "3D Printed Enclosure")  |  ![wood](./images/wood.jpg "Wood Enclosure")

## Assembly order

We recommend the following order to assemble this open-source project from scratch.

1. [Order components](#order-all-components)
2. [Solder board](#solder-components-onto-board)
3. [Configure wifi](#configure-wifi-chip)
4. [Configure MCU](#configure-microcontroller)
5. [Configure server](#configure-server)
6. [Print enclosure](#print-enclosure)

## Thanks

A big thank you to our instructor, Professor Ilya Mikhelson of Northwestern University. This webcam project was created as a part of his Engineering System Design sequence.

## Order components

We've included a [Bill of Materials](./BOM.txt) that details all of the components used in the project - primarily components to be soldered onto the board, but also some cabling to connect to it.

Here are some potential places to order from: [Mouser](http://www.mouser.com/), [Digikey](https://www.digikey.com/), [Robotshop](http://www.robotshop.com/).

You'll also need to order the PCB to solder the components onto. We've included our [raw EAGLE files](./board/), as well as a set of [Gerber files](./board/gerber_files.zip) that can be sent directly to the manufacturer.

## Solder board

If you have experience with surface mount soldering, this should be fairly straightforward. If you do not, we recommend practicing some before working with any expensive components. [This playlist](https://www.youtube.com/playlist?list=PL1ec5YBm_crySPZat6Y5e9hxfIUI7d97B) has many useful videos for this project, including several specifically on soldering.

We recommend the following soldering order to unit test each component before moving forward.

1. Power circuit: barrel jack, voltage regulator, power indicator LED, associated resistors and capacitors. Confirm that ground and power are not connected via continuity test. Plug in power cord and confirm that the power indicator lights up.

2. Microcontroller, PLL circuit, crystal oscillator, buttons, associated bypass capacitors, and header pins. After soldering the MCU, confirm each connection via continuity test to another via or pad, and confirm that no pins are connected to power or ground that shouldn't be. Connect the Atmel debugger, power the circuit, and confirm that the device signature can be read from Atmel Studio.

3. Wifi Chip, indicator LEDs, header pins, and associated bypass capacitors. Again, confirm each connection individually before proceeding. To confirm proper operation, perform step 4 to configure the chip.

4. Microphone and associated passive components.

5. Camera module and associated passive components. Be sure to solder the connector without the module inserted, and again confirm connections individually before proceeding.

## Configure wifi chip

The wifi chip requires a fair degree of configuration to work correctly. Zentri helpfully includes an OS to which commands are issued, and it's quite easy to work with their [command API](https://docs.zentri.com/zentrios/wz/3.3/cmd/commands). We recommend issuing these commands via a terminal emulator ([TeraTerm](https://ttssh2.osdn.jp/index.html.en) for windows or [CoolTerm](http://freeware.the-meiers.org/) for Mac) connected to the header pins for the UART communication via an FTDI cable. If you use the FTDI cable, make sure that the microcontroller will not be attempting to use the UART bus (either because no code has been loaded onto it or because it does not configure or use its UART module), or bus contention will result. These operations can also be performed by editing the MCU code and running commands, but that's much more difficult and harder to read/interpret feedback.

1. Connect to the chip. Confirm your connection by hitting Enter; the chip should respond with Ready.
2. If you're using a network which requires registration, obtain its mac address via the get wl m query, and register it to the network.
3. Connect the device to your network with the setup command. Once the command has been run, find the device's network on your computer. Connect to it, visit setup.com, and follow the prompts to connect to the desired network. Thereafter, the web console can be accessed from `http://zentrios-XYZ.local/`, where XYZ are the last 3 digits of your wifi chip's serial number.
4. Register with Zentri, and claim your device via the dms claim command.
5. Update the device's firmware with the OTA command.
6. Set the indicator GPIOs to indicate on the correct LEDs by running the following commands:

```
set sy i g wlan 20
set sy i g network 21
set sy i g softap 22
```

7. (Do this step only if you want to serve over HTTPS) Load TLS certificate onto the chip using its file uploader. Change its default TLS certificate to this file via the ne t a command.

8. Change the baud rate by setting the ua b variable for UART1. The default speed for UART1 is 115200, and our system is designed to run at 2.5Mbauds. You may want to turn the baud rate down in the MCU code to test operation before turning both up to this high speed - we haven't found a terminal emulator that can successfully interpret messages at this speed, and any issues can be difficult to debug.

## Configure microcontroller

To load our MCU code (found in [this separate repository](https://github.com/nathanshelly/webcam_mcu_code)) onto the microcontroller, first connect your Atmel debugger to the corresponding header pins and to your computer. Open our Atmel project, and connect to the debugger. Compile and load the code - it should run correctly now that the wifi chip is properly configured.

## Configure server

Our back-end stack consists of NGINX and Tornado on a Google Compute Engine instance. This could be hosted on any cloud provider or personal server. NGNIX directly handles requests for our static HTML, CSS and Javascript files while serving as a reverse proxy forwarding dynamic websocket requests to our Tornado server. We currently serve the website over HTTPS though this is not strictly necessary. If you plan to also serve over HTTPS our [NGINX configuration file](./nginx_config) would require only changing the server name in the port 80 and 443 blocks and then updating the ssl_certificate/key lines to point to your certificate. If you've never served over HTTPS before but would like to try we highly recommend getting a certificate from [LetsEncrypt](https://letsencrypt.org/) (they have great documentation and are free!).

If you want to serve over HTTP instead you'll need to move the location directives from the 443 block to the 80 block and remove the `return 301 https://$server_name$request_uri;` line which redirects all HTTP traffic to HTTPS. The ssl_certificate/key lines can also be removed.

The static files and our Tornado server file are all hosted in [this repository](https://github.com/nathanshelly/webcam_web_code) along with a more in-depth description of setting up the website.

## Print enclosure

For designing the enclosure we used an online program called [Onshape](https://www.onshape.com/). This provides a convenient way to edit CAD files with low overhead though it lacks some functionality from a fuller editor like Solidworks. View our [Onshape project here](https://cad.onshape.com/documents/ae298fb239b6988d4ccff146/w/2fbac33a8452420bf0238e8e/e/6b2309d9b32e628505d094b1).

We've also provided the raw files in two different formats. If you'd like to modify our design [here](./enclosure/editable) are .sldprt files. If you'd like to print our design as is [here](./enclosure/printable) are .stl files.

## Enjoy!
