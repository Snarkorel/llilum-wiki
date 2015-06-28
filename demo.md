# Demo 
The demo is implemented for a [NXP LPC1768 device](https://developer.mbed.org/platforms/mbed-LPC1768/), featuring 512KB of NOR FLASH and 64Kb of SRAM. 

The device is enabled for the Mbed ecosystem. Any other Mbed device could be used with minimal changes to the makefile at _\<repo\>_\\Zelig\\LLVM2IR_results\\mbed\\simple\\makefile.  

## Device setup
* Setup your device following instructions at https://developer.mbed.org/platforms/mbed-LPC1768/. This includes updating the device firmware to support the MTP driver and virtual serial port. 
* Install the virtual serial port driver for your Mbed device following instructions at https://developer.mbed.org/handbook/Windows-serial-configuration. 

## Build Zelig image
* Make the FrontEnd project the startup project in your solution, or simply run the FrontEnd executable from the command line
  * If using the solution, configure the debug properties as follows:  
    `cfg ..\..\..\..\Zelig\CompileTime\CodeGenerator\FrontEnd\mbed_simple.FrontEndConfig`  

![FrontEnd project debug configuration](https://github.com/NETMF/llilum-pr/wiki/FrontEndconfig.PNG)

  * If running from the command line, simply pass the same `-cfg` parameters pointing to the above mentioned _v_ configuration file 
  * The build output directory will be located in _\<repo\>_\\Zelig\\LLVM2IR_results\\mbed\\simple\\
* Open a command prompt, move to the root _\<repo\>_ directory, and run `setenv.cmd` to set local environment variables.
* Move to the build output directory _\<repo\>_\\Zelig\\LLVM2IR_results\\mbed\\simple\\ and run `build.bat` to invoke LLVM optimizer and compiler, and finally GCC 
  * GCC will link the object file created by LLVM into a simple runnable image 
  * Code size of entire executable and will be dumped and disassembly will be produced 
  * Build output will contain the following: 
    * **Microsoft.Zelig.Test.mbed.Simple.bc**: the LVM binary bit code 
    * **Microsoft.Zelig.Test.mbed.Simple.ll**: the LLVM readable bit code     
    * **Microsoft.Zelig.Test.mbed.Simple.TypeSystemDump.IrTxtpost**: Zelig readable IR with TS details 
    * **Microsoft.Zelig.Test.mbed.Simple.ZeligImage**: The Zelig image
    * **Microsoft.Zelig.Test.mbed.Simple.ZeligIR**: Zelig binary IR for reload
    * **Microsoft.Zelig.Test.mbed.Simple.ZeligIR_Post**: Zelig binary IR 
    * **Microsoft.Zelig.Test.mbed.Simple_opt.bc**: LLVM optimized bit code 
    * **Microsoft.Zelig.Test.mbed.Simple_opt.ll**: LLVM optimized readable bitcode 
    * **Microsoft.Zelig.Test.mbed.Simple_opt.o**: LLVM object code 
* Customize and invoke `deploy.bat` for your Mbed device
    * (each Mbed device creates a virtual serial port on your host system, you can discover which one using Device Manager. Simply run _devmgmt.msc_ on any Windows machine and look under the _Ports_ node) 
    * Deployment is a simple file copy through MTP, thanks to the Mbed bootloader 
    * When deploying the runnable image to the device, you will see the device blink 
    * Reset your device when deployment is complete (device stops blinking and copy completes) 
  * Your device is now ready for attaching a debugger

## Debugging
* To debug on the command line:
  * Launch the `debug.bat` script. The script will launch pyOCD and try to connect to remote target on port 3333. 
  * If the `debug.bat` script fails you can launch pyOCD manually [pyOCD](https://launchpad.net/gcc-arm-embedded-misc/pyocd-binary/), which will automatically detect and connect to the device using CMSIS-DAP, and then connect to the pyOCD debug server with command `target remote :3333`
* Now you can set a breakpoint with the command _b_main_ and gdb will show you are hitting function `Microsoft.Zelig.Runtime.Bootstrap::Initialization()`.  
    ![gdb debug window](https://github.com/NETMF/llilum-pr/wiki/GDBDebug.PNG)  
* To debug in Visual Studio 2015:
  * Launch VS from the command line so that it picks up your local environment variables.
  * Open the command window (View | Other Windows | Command Window) and enter the following command:  
    `Debug.MIDebugLaunch /OptionsFile:<repo>\Zelig\LLVM2IR_results\mbed\simple\DebugOptions.xml  /Executable:<repo>\Zelig\LLVM2IR_results\mbed\simple\LPC1768\mbed_simple.elf`

A rather complete GDB tutorial can be found at http://web.mit.edu/gnu/doc/html/gdb_1.html or in this [PDF](https://github.com/NETMF/llilum-pr/wiki/gdbTutorial.pdf) file.

Welcome to LLILUM!  



P.S. you can install the trial version of VisualGDB to use the Visual Studio UI and debug with the usual F5/10/11 key shortcuts. 





