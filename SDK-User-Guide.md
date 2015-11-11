Llilum SDK
================== 

The Llilum SDK is a set of tools that will allow users to get started working with Llilum

## Prerequisites
As of right now, the prerequisites for building and using the SDK are:
* Visual Studio 2015 installed on the machine
* Cross-platform development tools for Visual Studio for debugging purposes: [see prerequisites section](https://github.com/Microsoft/MIEngine/) 
* [Visual Studio Extensibility Tools](https://go.microsoft.com/fwlink/?LinkId=615455) 
* [Visual Studio Project System Extensibility Tools](http://aka.ms/vsprojectsystemextensibilityvsix)
* LLVM and ARM GCC from [setup steps](https://github.com/NETMF/llilum/wiki/Setup)

## Components 
1. The Llilum Compiler
2. LLVM Tools (referenced, not included in SDK) 
3. GCC for ARM (referenced, not included in SDK) 
4. Make for Windows (not needed for VS project) 
5. mBed Libraries for LPC1768 and K64F boards 
6. A basic "blinky" test project (not needed for VS project) 
7. Scripts for creating the binary to deploy on a device 
8. Visual Studio Llilum template and project type 

## Creating the SDK
In order to create the SDK, the user must first go through the [setup steps](https://github.com/NETMF/llilum/wiki/Setup). **Be sure to actually build the entire Llilum Solution!**  

Full details on building the SDK are located in the [SDK source readme.md](https://github.com/NETMF/llilum/tree/dev/LlilumSDK)

## Installing Llilum SDK for VS2015
1. If you have previously installed the Llilum SDK, open Visual Studio and go to ```Tools -> Extensions and Updates``` and remove the Llilum application VSIX.
2. Close all instances of Visual Studio to avoid having to restart your machine
3. Install Click on the MSI to install the SDK

## Install Visual Studio Extension
Install the Llilum Visual Studio project system and debugger extensions. This is isolated from the MSI to prevent binding the MSI to any particular Visual Studio Version. We may unify this with an installer executable in the future - for now they remain distinct.

If you are still unsure, verify the following:  
* The ARM GCC directory contains "bin", "lib", and "arm-none-eabi" folders

## Device setup
* Setup your device following instructions at https://developer.mbed.org/platforms/mbed-LPC1768/. This includes updating the device firmware to support the MTP driver and virtual serial port.  
* Install the virtual serial port driver for your Mbed device following instructions at https://developer.mbed.org/handbook/Windows-serial-configuration.  

## Creating a Llilum project
1. Open Visual Studio 2015 and open the New Project Dialogue
2. If the SDK installed properly, there should be a LlilumApplication project type
  a. **NOTE: This project needs to be in a directory without spaces!**
3. When the project opens, right click on the Native project and click properties. Click Llilum Configuration, select your board from the dropdown, and click apply.
4. Right click solution->Rebuild Solution, and it should start building! If an error dialogue appears, it means that the environment variables were not updated. Solutions include: 
  * Option 1 - Log out and back in
  * Option 2 - Open the Native project settings, go to Llilum Configuration, and set up all "General" section settings manually
  * Option 3 - Close all instances of Visual Studio, open task manager, right click on the explorer process, and click Restart (not guaranteed to work)
5. If the solution doesn't build, right click on the solution, go to project dependencies, select "Native" from the dropdown, check Managed in the list below, then click Apply

## Deploying and Debugging
1. To manually deploy, plug in the mBed device, go to ```<solution directory>\ARM\Debug``` and copy the <solution name>.bin file to the drive letter of the mBed device.
2. To deploy and debug in Visual Studio, click F5, or click the green play button.
3. A prompt may come up asking to permit the debugger execution. Click Allow
4. If debugging does not appear to be working, go to Native project properties, click the Debugging section, change the "Use flash tool" field to "No", and restart debugging.

Note: The green play button should have "Llilum Debugger" written next to it  

## Troubleshooting
**Prior to troubleshooting, ensure 2 things: the path to the SDK has no spaces and the path to your project has no spaces. Current limitations do not allow for this. This will be fixed in a future iteration.**  
  
**Building doesn't work**  
* **Build output says `<solution name>.exe` was not found:** Make sure to set solution dependencies so Native project has a dependency on Managed project
* **Build output shows missing header file:** Be sure to select your board under Native -> Properties -> Llilum Config
* **Build output still shows missing header file:** Check the paths in Llilum Config properties. Make sure they exist and point to the most current SDK  
  
**When starting debugging, an error dialog comes up**  
In this situation, close all instances of py_ocd and arm-gdb. These can be found in the task manager. If issue persists, restart Visual Studio, and do a clean build.  
  
**Debugging session starts, but breakpoints don't work**  
The most common cause is loading the wrong symbols (or incorrectly) for your current machine. Right click on the Native project, and go to Debugging section, and change "Use Flash Tool" to No. Try to re-deploy. If that doesn't work, reset your development board, check all paths in the Debugging section, and ensure that Llilum Debugger is the selected debugger.