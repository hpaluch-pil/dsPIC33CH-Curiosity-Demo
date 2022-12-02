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
- MPLAB X IDE `v5.50`
  > WARNING! Version v6.05 freezes right when Slave project is added to Master!
  > From that point even killing it and running again will not help - it will freeze again!
- DFP: `dsPIC33CH-MP_DFP `v1.7.194`
- XC16 `v.20`

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
  - in my case I have plugin `MCC v5.0.3` - latest version for MPLAB v5.50
- now click on `MCC` icon on Toolbar to open MCC Tool
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
- now Single click on this cell:
  - Row: `Pin Module` -> `GPIO output` 
  - Column: `Port E` -`0`
- the right cell should have now green Lock Icon
- now Single click on this cell for Slave LED (`RE1`):
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

Now rather close MCC tool by clicking on `MCC` icon in toolbar

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
  - temporarily keep CHECKED `Set as main project`
- click on `Finish`

Now we need a bit tricky way run `MCC` on `Slave.X` project:
- ensure that `Slave` is temporarily Main project (Bold font in Projects tab) 
- click on `MCC` icon in toolbar to run MCC tool on `Slave` project
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
- now SINGLE click on this cell:
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
- AGAIN WARNING! If you have MPLAB X IDE v6.05 it will likely freeze...
  - so use rather MPLAB X IDE v5.50 - it works fine.

Now we need to tell MPLAB to build Slave project:
- right-click `Master` -> `Secondaries` -> `Properties...`
  - WARNING do NOT right-click on `Slave` leaf - there is no `Properties...` dialog.
    You have to click on parent `Secondaries`
- select checkbox `Build` and click `OK` to close dialog.

Finally we have to copy and paste example sources
to `Master.X/main.c` and `Slave.X/main.c` as shown
on https://microchipdeveloper.com/16bit:ch-example

Please see:
- [Master.X/main.c](Master.X/main.c)
- [Slave.X/main.c](Slave.X/main.c)
for full source.


# Building

In Tab `Projects` right-click on `Master` and select `Set as Main Project` (it must be that
way before build).

Now you can right-click on `Master` and select `Clean and Build` - or use Hammer icons on Toolbar.
- Please note that this will ALSO build `Slave` project, because it has to be included in Master Core Program
  Flash.

# Programming and running

If you have already assigned embedded PicKit programmer you can just use
- `Run Main Project` (big green arrow icon on toolbar)

- Plase be patient - it may take some time for program to run...

