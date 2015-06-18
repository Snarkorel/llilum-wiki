## DistanceMeasurement demo project
The prototype code I am using for my demonstration project is in <root>\Prototypes\CS\DistanceMeasurement. There is enough documentation in Program.cs that you should be able to replicate the circuit. The CS project should take the place of the direct LLVMIR generation code in <root>\prototypes\DistanceMeasurement, and plug into the <root>\LLVMHost component that will be compiled with gcc into the .bin file that will be deployed to the device.

## Generating DistanceMeasurement ZIR
To see the Zelig IR for the demo project:
* open <root>\llvm_ir_generator\llvm_ir_generator.sln
* build the Debug target
* add "-Cfg ..\..\..\prototypes\CS\DistanceMeasurement\DistanceMeasurement.config" to the commandline arguments in the debug pane of the **FrontEnd** project
* output will be in <root>\zelig\Tools\Build\DistanceMeasurement_prototype\DistanceMeasurement_1.0.0.0.txt

## Required Setup (if you want to see the LLVMIR generation, LLVMHost project, and a functioning demo)
I have merged in LLVM branch into this branch. See the [LLVMIR to Native...](https://github.com/MSOpenTech/il2n-pr/wiki/LLVMIR-to-Native-demo-project-and-hardware-configuration) wiki entry for setup.

Define environment variables LLVM_INCLUDE and LLVM_LIBS to point to include files and libraries for LLVM.  LLVM_LIBS should *not* include the flavor of the bits (Debug or Release). 

## Converting IL to IR: 
To see this in action:

1. open llvm_ir_generator\llvm_ir_generator.sln
2. navigate to Tools-->Converters-->il2ir
3. set il2iras the default project
4. build Debug
5. add "-Cfg ..\..\..\prototypes\CS\DistanceMeasurement\DistanceMeasurement.config" to the command line arguments in the debug tab
6. Run

IR Translation is handled in **LLVMIRTranslator.cs**. 
The Visitor implementation is in **Visitor.cs**.
Interop with the LLVM object model is handled in the LLVMIR C++/CLI project.

Right now the project takes the IL produced by the DistanceMeasurement project and produces a .o file with the static functions returning void. Opcodes that are not currently being translated are being replaced with NOPs, and console output is generated describing the substitution.

There is lots of work to be done to flesh this out.