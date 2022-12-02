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

Now we have to run MCC (Microchip Configuratin tool)
- ensure that you have installed MCC in MPLAB X IDE
  - go to `Tools` -> `Plugins`
  - seect `Installed` tab and look for `MPLAB Code Configurator`
  - if it is not there - select tab `Available Plugins` and select it
  - in my case I have plugin `MCC v5.2.2`
- now click on `MCC` icon on Toolbar to open MCC Tool
- in my case (MPLAB X IDE v6.05 and MCC v5.2.2) you may get redirected to issue:
  - https://onlinedocs.microchip.com/pr/GUID-BD1BECBF-14DB-4FBB-82D3-A4219CF22F9F-en-US-1/index.html?GUID-67D73C1F-567E-4BDF-B437-3F3CB8251844
- now we will follow guide and so:
- `MCC Content Manager` window should appear
- click on `Select MCC Classic`
- click on `Finish` - it will download required libraries and finally run MCC Tool
- now select `Resource Management` tab
- under window `Device Resources` expand `Peripherals` -> `SLAVE CORE` -> `Slave Core`
- either click on `+` icon or double-click `Slave Core` to add it to Project
- fill in to Slave Project Name: `Slave` (we will later create `Slave.X` project that must match this name)
- for sake of example we have to do this under `Master Slave Interface` -> `Mailbox Configuration`
- enable `Protocol A` and `Protocol B`
- in case of `Protocol A` change Direction to: `M -> S` (was `S -> M`)
- the `Protocol B` should be kept to `S -> M`
- both should have Buffer Size 2 bytes (default)

Now we have to assign these LEDs from above table to proper Master and/or Slave core:

| Peripheral | dsPIC33CH pin and/or port |
| --- | --- |
| LED1 red | RE0 |
| LED2 red | RE1 |

So do this:
- select `Pin Manager: Grid View` (it is in right-bottom Window)
- now double click on this cell:
  - Row: `Pin Module` -> `GPIO output` 
  - Column: `Port E` -`0`
- the right cell should have now green Lock Icon
- now double click on this cell for Slave LED (`RE1`):
  - Row: `Slave Core` -> `Ownership` -> `output`
  - Column: `Port E` -> `1`
- now select `Pin Module` tab (top middle Window)
- and on `RE0` set Custom Name: `LED_MASTER`

Now we have to save Master settings from Slave core (yes :-)
- select tab `Slave Core`
- click on `Save Master Settings`
- this should save `.mc3` files under `Master.X` project

Now we can click on Project Resources -> Generate to generate C source files
under `Master.X/mcc_generated_files/`
- ignore warnings so far

Do NOT build yet - we need to create Slave project for build to succeed.

Back in MPLAB X IDE we have to create Slave project:

- click on `File` -> `New Project...`
- select `Microchip Embedded` -> `Standalone Project`, click `Next`
- select:
  - Family: `16-bit DSCs (dsPIC33)`
  - Device: `dsPIC33CH512MP508S1` (SLAVE - must end with `S1`!)
  - Tool: `No tool` - does not matter unless you want to debug BOTH Cores,
    in which case you need 2nd Debugger
    (slave is programmed from Master Core and its Master flash)
- click `Next`
- select XC16 compiler - in my case `v2.00`
- click `Next`
- NOW important:
  - select location for Slave project - it must be on same level where is `Master.X` project (!)
- I selected:
  - project Name: `Slave` - the name must match those configured in MCC Tool (!)
  - project Location: `E:\projects\dsPIC33CH-Curiosity-Demo`
  - resulting project folder `E:\projects\dsPIC33CH-Curiosity-Demo\Slave.X`
  - UNCHECK `Set as main project` (slave will be build as part of Master build)
- click on `Finish`

Now we need a bit tricky way run `MCC` on `Slave.X` project:
- temporarily right-click `Slave` and select `Set as Main project`
- Close and Open MCC Tools 
- again select `MCC Classic` and `Finish`

Loading Master settings to Slave project:
- select tab `Master Core`
- select `Easy Setup` sub-tab
- click on `Load Slave Settings from Master Configuration`
- when asked for `.mc3` file select `e:\projects\dsPIC33CH-Curiosity-Demo\Master.X\master_config.mc3`
- don't be scared with RB1 pin conflict.
- click on YES to override `RB1` pin setting

Now we have to properly configure and name `RE1` (in Master config we just reserved
id as "Slave output" but detailed configruation must be done for Slave core:
- in Pin Manager: `Grid View`
- now double click on this cell:
  - Row: `Pin Module` -> `GPIO output` 
  - Column: `Port E` -`1`
- there must show green lock icon in this cell.
- now in `Pin Module` set Custom Name `LED_SLAVE` for `RE1` pin.


Now we can click on Project Resources -> Generate to generate C source files
under `Slave.X/mcc_generated_files/`

Now we need to understand how Dual Core CPU is programmed and executed:
1. Master Core flash contains code for BOTH Master and Slave CPU
1. Master Core copy Slave program to Slave Program RAM (PRAM)
1. master Core starts Execution of Slave

Therefore there is only Single Program (hex) file that must include both Master and Slave
Core code. So we have to include `Slave.X` project into `Master.X` project build using:
- select tab `Projects`
- right-click on `Master` -> `Secondaries` (was `Slaves` in older MPLAB X IDE) and
- click on `Add Secondary Project...`
- select `E:\projects\dsPIC33CH-Curiosity-Demo\Slave.X`
- ensure that Store path as: is `Relative` (to make this project portable)
- and click on `Add`

Wow! In my case MPLAB X IDE v6.05 froz...





