# Front End Configuration

The compiler tool in the Zelig system is located at the [FrontEnd](https://github.com/MSOpenTech/il2n-pr/tree/il2ir_demo/zelig/Zelig/CompileTime/CodeGenerator/FrontEnd) project. 

A typical configuration file for the compiler looks like a list of instructions, path and assembly references. This is the configuration file for the LLVM Demo for LPC1768 Mbed dev board named _mbed_simple.FrontEndConfig_: 

```
###
### Location of the Zelig assemblies.
###
-HostAssemblyDir   ..\..\..\..\ZeligBuild\Host\bin\Debug
-DeviceAssemblyDir ..\..\..\..\ZeligBuild\Target\bin\Debug

-CompilationSetup Microsoft.Zelig.Configuration.Environment.LPC1768MBEDHostedCompilationSetup
#-CompilationSetup Microsoft.Zelig.Configuration.Environment.K64FMBEDHostedCompilationSetup


###
### We need to include this assembly to get the right drivers.
###
-Reference Microsoft.CortexM3OnMBED
-Reference Microsoft.CortexM3OnCMSISCore
-Reference Microsoft.DeviceModels.ModelForCortexM3
-Reference LPC1768

#-Reference Microsoft.CortexM4OnMBED
#-Reference Microsoft.CortexM4OnCMSISCore
#-Reference Microsoft.DeviceModels.ModelForCortexM4
#-Reference K64F


###
### Add compilation phases, in order
###
#-CompilationPhaseDisabled ReduceNumberOfTemporaries
#-CompilationPhaseDisabled TransformFinallyBlocksIntoTryBlocks
#-CompilationPhaseDisabled ApplyClassExtensions
#-CompilationPhaseDisabled PrepareImplementationOfInternalMethods  
#-CompilationPhaseDisabled CrossReferenceTypeSystem
#-CompilationPhaseDisabled ApplyConfigurationSettings
-CompilationPhaseDisabled ResourceManagerOptimizations
#-CompilationPhaseDisabled HighLevelTransformations
#-CompilationPhaseDisabled PropagateCompilationConstraints
#-CompilationPhaseDisabled ComputeCallsClosure
#-CompilationPhaseDisabled EstimateTypeSystemReduction
#-CompilationPhaseDisabled CompleteImplementationOfInternalMethods
#-CompilationPhaseDisabled ReduceTypeSystem
-CompilationPhaseDisabled PrepareExternalMethods
#-CompilationPhaseDisabled DetectNonImplementedInternalCalls
#-CompilationPhaseDisabled OrderStaticConstructors
#-CompilationPhaseDisabled LayoutTypes
#-CompilationPhaseDisabled HighLevelToMidLevelConversion
-CompilationPhaseDisabled FromImplicitToExplictExceptions
-CompilationPhaseDisabled MidLevelToLowLevelConversion
-CompilationPhaseDisabled ConvertUnsupportedOperatorsToMethodCalls
-CompilationPhaseDisabled ExpandAggregateTypes
-CompilationPhaseDisabled SplitComplexOperators
-CompilationPhaseDisabled FuseOperators
-CompilationPhaseDisabled Optimizations
-CompilationPhaseDisabled ConvertToSSA
#-CompilationPhaseDisabled GenerateImage
-CompilationPhaseDisabled PrepareForRegisterAllocation
-CompilationPhaseDisabled CollectRegisterAllocationConstraints
-CompilationPhaseDisabled AllocateRegisters
-CompilationPhaseDisabled ConvertToLLVMIntermediateRepresentation
#-CompilationPhaseDisabled Done

###
### The program to compile.
###
..\..\..\..\ZeligBuild\Target\bin\Debug\Microsoft.Zelig.Test.mbed.Simple.exe

###
### Where to put the results.
###
-OutputName Microsoft.Zelig.Test.mbed.Simple
-OutputDir  ..\..\..\..\LLVM2IR_results\mbed\simple

-DumpIR
#-DumpIRpre
#-DumpIRpost
#-DumpIRXML
-DumpLLVMIR
#-ReloadState
-DumpLLVMIR_TextRepresentation

-MaxProcs 1

-NoSDK
```
## Keywords  
* **\#**: marks the start of a comment line
* **-Reference** \<_assembly_\>: Instruct Zelig to load _assembly _as a reference for the application. This includes device drivers as well as system assemblies. 
* **-HostAssemblyDir** \<_directory_\>: location of assemblies to be used for by the compiler, e.g. for loading phases or code transformations. 
* **-DeviceAssemblyDir** \<_directory_\>: location of assemblies to be used for by the compiler, e.g. for loading phases or code transformations. 
* **-CompilationPhaseDisabled** \<_phase_\>: instructs the compiler to skip the phase _phase_. 
* **-OutputName** \<_name_\>: output image name
* **-OutputDir** \<_dir_\>: output directory
* **-DumpIR**: instructs the compiler to dump the IR after generating the image 
* **-DumpIRpre**: instructs the compiler to dump the IR before applying any code transformation 
* **-DumpIRpost**: instructs the compiler to dump the IR after applying all transformations, inclusive of the full TS
* **-DumpLLVMIR**: instructs the compiler to dump the IR after applying all transformations 
* **-ReloadState**: instructs the compiler to reload the last persisted IR. Useful to debug and develop LLVM IR translation. 
* **-DumpLLVMIR_TextRepresentation**: instructs the compiler to dump the LLVM IR in readable format.
* **-MaxProcs** \<_n_\>: instructs the compiler to use _n_ threads to compile

[==> Setup and Build Instructions](https://github.com/NETMF/llilum-pr/wiki/Setup)

