# MSI Demo project for dsPIC33CH

Master-Slave-Interface introductory demo project for
dual-core dsPIC33CH from Microchip tutorial:
- https://microchipdeveloper.com/16bit:ch-example

Hardware Requirements:
- [dsPIC33CH Curiosity Development Board](https://www.microchip.com/en-us/development-tool/DM330028-2)
- Micro USB cable (there is no one included with board!)
- ensure that you downloaded data-sheet from:
  - https://ww1.microchip.com/downloads/en/DeviceDoc/DS50002801%20-%20dsPIC33CH%20Curiosity%20Development%20Board%20Users%20Guide.pdf
- Notice dsPIC type from data-sheet:
  - [dsPIC33CH512MP508](https://www.microchip.com/en-us/product/dsPIC33CH512MP508)

Here is brief overview of I/O peripherals - excluding DC/DC converter parts:

| Peripheral | dsPIC33CH pin and/or port |
| --- | --- |
| S1 push-button | RE7 |
| S2 push-button | RE8 |
| S3 push-button | RE9 |
| S4 push-button | /MCLR (reset) |
| R/G/B LED | RB14/RD7/RD5 |
| LED1 red | RE0 |
| LED2 red | RE1 |
| 10kOhm pot | RA0 |


Software Requirements:
- MPLAB X IDE `v5.05`
- DFP: `dsPIC33CH-MP_DFP `v1.7.194`
- XC16 `v.200`

# Step by Step tutorial

We will mostly follow 
- https://microchipdeveloper.com/16bit:ch-example
Adjusting it for Curiosity board and parts.

To create Master core project do this:
- connect your Curiosity board to PC with USB micro cable 
  - se we will be able to select Programming tool while creating new project.
- run MPLAB X IDE


Now we will create project for Master Core:
- click on `File` -> `New Project...`
- select `Microchip Embedded` -> `Standalone Project`, click `Next`
- select:
  - Family: `16-bit DSCs (dsPIC33)`
  - Device: `dsPIC33CH512MP508` (MASTER - without `S1`!)
  - Tool: `Starter Kits (PKOB)-SN:BURxxx` (you need to have your Curiosity board connected to PC for this)
- click `Next`
- select XC16 compiler - in my case `v2.00`
- click `Next`
- NOW important:
  - select location for Master project - please note that we will later create 2nd (Slave) project on same level
  - do NOT use dash (`-`) or spaces in project name! It is later converted to Symbol name which will crash build!
- I selected:
  - project Name: `Master` (again - do NOT use dashes (-))
  - project Location: `E:\projects\dsPIC33CH-Curiosity-Demo`
  - resulting project folder `E:\projects\dsPIC33CH-Curiosity-Demo\Master.X`
  - keep `Set as main project` checked on.
- click on `Finish`


