# Setup and Build Instructions

## Setup

Pre-requisites are  
1. Visual Studio 2013  
2. LLVM 3.5 or above   
3. [Make for Windows](http://gnuwin32.sourceforge.net/packages/make.htm) to build with GCC  

To build LLVM, one needs to install [CMake](http://www.cmake.org/download/) and add it to the path. 
To build LLVM, please follow instructions [here](http://llvm.org/) or [here](http://llvm.org/docs/GettingStarted.html). 

The instructions below will refer to the mandatory as well optional steps. Optional steps are marked clearly and refer to, for example, how to build/install LLVM, should one not have done that yet. 

* Clone the entire repo with: _git clone https://github.com/NETMF/zelig-pr.git_ into directory _\<repo\>_  
* [OPTIONAL] Move to directory _\<repo\>_\\external_ and install LLVM using the script located in that directory. You will need CMake to complete this step. setup the following environment variables:   
```
 LLVM_BIN=_\<repo\>_\\external\\LLVM\\Debug\\bin\\  
 LLVM_INCLUDE=_\<repo\>_\\external\\LLVM\\include\\  
 LLVM_LIBS=_\<repo\>_\\external\\LLVM\\  
```
  * change LLVM header file IRbuilder.h. line 74, from 

`InsertPt = nullptr;`  to  
`InsertPt = BasicBlock::iterator(nullptr);`  

* install GCC from the same directory usign the other installation installation script. Set up the following environment variable for GCC

```
GCC4MBED_DIR=_\<repo\>_\\external\\gcc4mbed
```


* Move to directory _\<repo\>_\\Zelig\Zelig and pen Zelig solution _Zelig.sln_ 
  * build LLVMIR project first under code transformations, it will take a while 
  * build the rest of the solution 
* You are now ready to try the system on a real device! 

Go to [Build and Run Test Demo](https://github.com/NETMF/zelig-pr/wiki/demo) 
