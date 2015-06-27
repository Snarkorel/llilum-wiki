# Setup and Build Instructions

## Setup

Prerequisites:

1. Visual Studio 2013
2. LLVM 3.5
3. Python [2.7.4](https://www.python.org/downloads/release/python-2710/) installed and added to PATH
4. [ARM GCC](https://launchpad.net/gcc-arm-embedded) installed and added to PATH
5. [Make for Windows](http://gnuwin32.sourceforge.net/packages/make.htm) to build with GCC

To build LLVM, one needs to install [CMake](http://www.cmake.org/download/) and add it to the path. 
To build LLVM, you may follow instructions [here](http://llvm.org/) or [here](http://llvm.org/docs/GettingStarted.html), but it is best to use the .zip file and script mentioned below. (some LLVM C++11 constructs do not work well with our managed C++ wrapper...)

The instructions below will refer to the mandatory as well optional steps. Optional steps are marked clearly and refer to, for example, how to build/install LLVM, should one not have done that yet. 

* Clone the entire repo with: _git clone https://github.com/NETMF/zelig-pr.git_ into directory _\<repo\>_, e.g. ```c:\src\zelig-pr\```>"
* [OPTIONAL] Open a VS2013 CMD prompt and move to directory _\<repo\>_\\external and install LLVM using the script located in that directory. You will need CMake to complete this step.
* Change LLVM header file IRbuilder.h. line 74, from  `InsertPt = nullptr;`  to  `InsertPt = BasicBlock::iterator(nullptr);`
* Open a new CMD shell, and run setenv.cmd in _\<repo\>_ root (this will set LLVM environment variables)
* In the same CMD shell, move to directory _\<repo\>_\\Zelig\Zelig and open Zelig solution _Zelig.sln_, e.g. with ```c:\src\zelig-pr\zelig\Zelig>"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" Zelig.sln```
  * Build LLVMIR project first under code transformations; it will take a while.
  * Build the rest of the solution.
* You are now ready to try the system on a real device! 

Go to [Build and Run Test Demo](https://github.com/NETMF/zelig-pr/wiki/demo) 
