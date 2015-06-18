# Demo 
The demo is implemented for a [NXP LPC1768 device](https://developer.mbed.org/platforms/mbed-LPC1768/), featuring 512KB of NOR FLASH and 64Kb of SRAM. 

The device is enabled for the Mbed ecosystem. Any other Mbed device could be used with minimal changes to the makefile at _\<repo\>_\\Zelig\\LLVM2IR_results\\mbed\\simple\\makefile.  

## Device setup
* Setup your device following instructions at https://developer.mbed.org/platforms/mbed-LPC1768/. This includes updating the device firmware to support the MTP driver and virtual serial port. 
* Install the virtual serial port driver for your Mbed device following instructions at https://developer.mbed.org/handbook/Windows-serial-configuration. 

## Build Zelig image
* Make the FrontEnd project the startup project in your solution, or simply run the FrontEnd executable from the command line
  * if using the solution, configure the debug properties as follows: `cfg ..\\..\\..\\..\\Zelig\\CompileTime\\CodeGenerator\\FrontEnd\\mbed_simple.FrontEndConfig`  

![FronEnd proje debug configuration](https://github.com/NETMF/zelig-pr/wiki/FrontEndconfig.PNG)

  * if running from the command line, simply pass the same `-cfg` parameters pointing to the above mentioned _v_ configuration file 
  * The build output directory will be located in _\<repo\>_\\Zelig\\LLVM2IR_results\\mbed\\simple\\
* move to the build output directory _\<repo\>_\\Zelig\\LLVM2IR_results\\mbed\\simple\\ and run `build.bat` to invoke LLVM optimizer and compiler, and finally GCC 
  * GCC will link the object file created by LLVM into a simple runnable image 
  * code size of entire executable and will be dumped and disassembly will be produced 
  * build output will contain the following: 
    * **Microsoft.Zelig.Test.mbed.Simple.bc**: the LVM binary bit code 
    * **Microsoft.Zelig.Test.mbed.Simple.ll**: the LLVM readable bit code     
    * **Microsoft.Zelig.Test.mbed.Simple.TypeSystemDump.IrTxtpost**: Zelig readable IR with TS details 
    * **Microsoft.Zelig.Test.mbed.Simple.ZeligImage**: The Zelig image
    * **Microsoft.Zelig.Test.mbed.Simple.ZeligIR**: Zelig binary IR for reload
    * **Microsoft.Zelig.Test.mbed.Simple.ZeligIR_Post**: Zelig binary IR 
    * **Microsoft.Zelig.Test.mbed.Simple_opt.bc**: LLVM optimized bit code 
    * **Microsoft.Zelig.Test.mbed.Simple_opt.ll**: LLVM optimized readable bitcode 
    * **Microsoft.Zelig.Test.mbed.Simple_opt.o**: LLVM object code 
* customize and invoke `deploy.bat` for your Mbed device
    * (each Mbed device creates a virtual serial port on your host system, you can discover which one using Device Manager. Simply run _devmgmt.msc_ on any Windows machine and look under the _Ports_ node) 
    * deployment is a simple file copy through MTP, thanks to the Mbed bootloader 
    * when deploying the runnable image to the device, you will see the device blink 
    * reset your device when deployment is complete (device stops blinking and copy completes) 
  * your device is now ready for attaching a debugger
* open and customize the script `debug.bat` to target the correct virtual serial port 
    * the script will invoke gdb to aremotely attach on the specified COM port
    * your output on GDB command line will show you are hitting a debug break: 

`(gdb) target remote com3`  
`Remote debugging using com3`  
`start () at gcc4mbed.c:40`  
`40                  __debugbreak();`  
`=> 0x0000017a <\_start+22>      00 be   bkpt    0x0000 `     

* a rather complete GDB tutorial can be found at http://web.mit.edu/gnu/doc/html/gdb_1.html or in this [PDF]((https://github.com/NETMF/zelig-pr/wiki/gdbTutorial.pdf) file   
* now you can set a breakpoint with command _b main_ and then issue a _continue_ command   
  * gdb will show you are hitting function `Microsoft.Zelig.Runtime.Bootstrap::Initialization()`  

![gdb debug window](https://github.com/NETMF/zelig-pr/wiki/GDBDebug.PNG)  


Welcome to Zelig!  



P.S. you can install the trial version of VisualGDB to use the Visual Studio UI and debug with the usual F5/10/11 key shortcuts. 





