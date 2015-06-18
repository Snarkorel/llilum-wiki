# Setup and Build Instructions

## Setup
* Clone the entire repo with: _git clone https://github.com/NETMF/zelig-pr.git_ into directory _\<repo\>_
* Move to directory _\<repo\>_\\external_ and install GCC and LLVM using the scripts located in that directory
* setup the following environment variables:   
  * LLVM_BIN=_\<repo\>_\\external\\LLVM\\Debug\\bin\\  
  * LLVM_INCLUDE=_\<repo\>_\\external\\LLVM\\include\\  
  * LLVM_LIBS=_\<repo\>_\\external\\LLVM\\  
  * GCC4MBED_DIR=_\<repo\>_\\external\\gcc4mbed
* change LLVM header file IRbuilder.h. line 74, from 

`InsertPt = nullptr;`  to  
`InsertPt = BasicBlock::iterator(nullptr);`  

* move to directory _\<repo\>_\\Zelig\ext-tools and build the a few projects in the directories below, by opening their solution with Visual Studio and building it. If asked to upgrade the project, just do it and ignore any errors or warnings. 
  * GLEE 
  * JLINK  
  * CSharpTools\Parser  
  * TargetAdapters  
  * binutils 
* Move to directory _\<repo\>_\\Zelig\Zelig and pen Zelig solution _Zelig.sln_ 
  * build LLVMIR project first under code transformations, it will take a while 
  * build the rest of the solution 
* You are now ready to try the system on a real device! 

Go to [Build and Run Test Demo](https://github.com/NETMF/zelig-pr/wiki/demo) 
