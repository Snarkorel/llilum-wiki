In order to deploy and debug code on STM32 boards (correctly), ensure that you are observing the following guidelines:  
  
### Look at the README for OpenOCD support
This README is located under `<repo root>\Zelig\tools\openocd`

### Download and install ST-Link v2 Utility and drivers
This can be found here: http://www.st.com/web/en/catalog/tools/PF258168

### Download and install OpenOCD for windows
Links can be found here: http://openocd.org/getting-openocd/ Place the contents of the compressed file into this directory `<repo root>\Zelig\tools\openocd` and into `<SDK drop>\tools\openocd` if your SDK is already installed

### Uninstall and re-install the SDK extension
In Visual Studio, go to Tools->Extensions and Updates and uninstall the Llilum SDK VSIX, then follow the installation steps in https://github.com/NETMF/llilum/wiki/SDK-User-Guide

### Set up the appropriate configurations in the SDK project
* In Program.cs, uncomment the appropriate "#define" for the board you are using
* Right click on the Native project, and go to properties
* In the Debugging tab, ensure that GDB Server is set to OpenOCD and Deploy Tool is ST-Link v2 Utility
* Verify that the STLink-Utility Path is correct
* In the Llilum Configuration tab, ensure that the correct board is selected