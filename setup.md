# Setup and Build Instructions

## Setup

### Prerequisites:
1. Visual Studio 2015 (RTM)
2. LLVM 3.8.1 Release Distribution \[ [download]( http://llvm.org/releases/download.html#3.8.1 ) \] or clone the LLVM [repo] (https://github.com/llvm-mirror/llvm) and build it for Visual Studio 2015
3. Python [2.7.4](https://www.python.org/downloads/release/python-2710/) installed and added to PATH
4. [ARM GCC](https://launchpad.net/gcc-arm-embedded) installed and added to PATH
5. [Make for Windows](http://gnuwin32.sourceforge.net/packages/make.htm) to build mBed sample code for target devices with GCC

## Build

### Building required LLVM binaries:
###### (one time activity per LLVM version)
To build the Llilum source code you will need to download the [3.8.1 sources](http://llvm.org/releases/3.8.1/llvm-3.8.1.src.tar.xz) of LLVM and [Clang sources](http://llvm.org/releases/3.8.1/cfe-3.8.1.src.tar.xz).
> NOTE: Use the links provided here to get the LLVM source code as alternate sites, including the Mirror on GitHub may not be the correct version. In fact, the GitHub Mirror will NOT be as it tracks the head of the development branch for the currently unreleased version rather than the latest stable released version.

Put Clang sources to `<LLVM_SRC_ROOT>\tools\clang` (e.g. `C:\LLVM\3.8.1\tools\clang`)
To build LLVM source code, you need to install [CMake](http://www.cmake.org/download/) and add it to your system's PATH environment variable. 
To build LLVM, you may follow instructions [here](http://llvm.org/) or [here](http://llvm.org/docs/GettingStarted.html), but it is best to use the [BuildLlvmWithVS.cmd script file](https://github.com/NETMF/llilum/blob/dev/Zelig/Zelig/CompileTime/Llvm.NET/LibLLVM/BuildLlvmWithVS.cmd) provided in the Zelig repository.

1. Simply copy `BuildLlvmWithVS.cmd` (located under `<repo>\Zelig\Zelig\CompileTime\Llvm.NET\LibLLVM\`) into the root of your LLVM source tree for version 3.8.1
2. run `BuildLlvmWithVS.cmd`
 * This will build LLVM for multiple platform and configuration combinations (\< x86 | x64 \> + \< Debug | Release | MinRelDebInfo\> ).
 * _Depending on your hardware this can take anywhere from 3-6 hours to run and creates ~20GB of binaries. Thus, for teams it is recommended to do it once and share the LLVM source root on your network so everyone doesn't need to do that and store all the results._  
* The BuildllvmWithVs.cmd script will perform the following steps:  
 1. Run cmake to generate the necessary VS project and solution files  
 2. Build the solutions with the various configurations  
 3. Create a registry entry (under the per user registry HKEY_CURRENT_USER) to mark the location of the LLVM source root for this version of LLVM (that option requires running script with administrator privileges).
   * This is used to allow the LlvmApplication.props property sheet find the LLVM distribution to use. You can also set environment variable(s) to customize the paths used for LLVM if you choose to build it in a different manner. For details see the [LlvmApplication.props ]( https://github.com/NETMF/llilum/blob/dev/Zelig/Zelig/CompileTime/Llvm.NET/LibLLVM/LlvmApplication.props) property sheet.
    * If you are using a common shared folder for the LLVM binaries you can run `BuildllvmWithVs.cmd -g- -b-` to skip the cmake and build phases and just create a registry entry for your local machine that points to the shared location. _**Note: This only works for mapped drives as you can't open a command prompt to a UNC network share.**_ Alternatively you may choose to set environment variables for the location of the LLVM files. The most common one is LLVM_SRCROOT_DIR which should point to the root of your LLVM source tree wherever it was installed. If you build LLVM binaries in some fashion other than the provided BuildllvmWithVs.cmd script you will likely need to provide additional Environment variables for the tools to build. (For details see the [LlvmApplication.props ]( https://github.com/NETMF/llilum/blob/dev/Zelig/Zelig/CompileTime/Llvm.NET/LibLLVM/LlvmApplication.props) property sheet. ) The goal here is to allow the flexibility of fine control, if needed, or desired, while covering the 90+% use case automatically. 

### Building the Code Transformation Tools
1. Clone the entire repo with: `git clone https://github.com/NETMF/llilum.git` into directory `<repo>`, e.g. ```c:\src\llilum\```>"
2. Install GCC from link above and define a ```GCC_BIN``` environment variable to point to the _arm-none-eabi-xxx_ tools, e.g. ```set GCC_BIN=e:\tools\compilers\gcc\4_9_2015q2\bin```  
3. Open the Zelig solution `<repo>\Zelig\Zelig\Zelig.sln` 
NOTE: please note that the [SDK projects](https://github.com/NETMF/llilum/tree/dev/Zelig/Zelig/Test/mbed/SimpleSDK) in the _Test_ folder can only load if the LLILUM SDK for Visual Studio is installed. Building and deploying those projects is disabled by default, so after installing the SDK one still needs to modify the solution configuration to enable building the Simple managed project and deploying the Native project located [here](https://github.com/NETMF/llilum/tree/dev/Zelig/Zelig/Test/mbed/SimpleSDK). Please use the menu _Build => Configuration Manager_ to activate such options. 
4. Build the solution ( **Build** | **Build Solution** ). The property sheet should allow Visual Studio to find the LLVM binaries.
5. You are now ready to try the system on a real device! 

#### Building a sample target application
To build a sample application manually, see [Build and Run Test Demo](https://github.com/NETMF/llilum/wiki/demo)  
To get started with Visual Studio and the Llilum SDK, see [SDK User Guide](https://github.com/NETMF/llilum/wiki/SDK-User-Guide)

[==> Demo](https://github.com/NETMF/llilum/wiki/Demo)