# Depot layout


\<Zelig\>  
├───CompileTime  _\<\< \<\< \<\< \<\< \<\< \<\< \<\<  Zelig Compiler_  
│   ├───AnalysisTools  
│   │   ├───InequalityGraphVisualization  
│   │   ├───IRCompare  
│   │   └───IRViewer  
│   ├───CodeGenerator  
│   │   ├───CodeGenerator.UnitTest  
│   │   ├───CodeTransformation  
│   │   │   ├───Abstractions  
│   │   │   ├───Annotations  
│   │   │   ├───CompilationSteps  
│   │   │   │   ├───Attributes  
│   │   │   │   ├───Handlers  
│   │   │   │   ├───PhaseDrivers  
│   │   │   │   └───Phases  
│   │   │   ├───ControlFlowGraphState  
│   │   │   ├───DataFlow  
│   │   │   │   └───ControlTree    
│   │   │   ├───Expressions  
│   │   │   ├───ExternalMethodImporters  
│   │   │   ├───ImageBuilders  
│   │   │   │   └───ImageAnnotations  
│   │   │   ├───LLVM  
│   │   │   ├───Operators  
│   │   │   │   ├───Calls  
│   │   │   │   └───Control  
│   │   │   ├───Properties  
│   │   │   └───Transformations  
│   │   │       └───TypeSystemIntrospection  
│   │   ├───FrontEnd  
│   │   ├───IntermediateRepresentation  
│   │   │   ├───Annotations  
│   │   │   ├───BasicBlocks  
│   │   │   │   └───Cloning  
│   │   │   ├───ByteCode  
│   │   │   ├───Expressions  
│   │   │   ├───Operators  
│   │   │   │   ├───Call  
│   │   │   │   ├───Checks  
│   │   │   │   ├───Control  
│   │   │   │   ├───Memory  
│   │   │   │   ├───Object  
│   │   │   │   └───TypedReference  
│   │   └───LLVMIR  
│   ├───MetaData  
│   │   ├───Importer  
│   │   │   ├───PDBInfo  
│   │   ├───MetaData.UnitTest  
│   │   └───Normalized    
│   └───TargetModels  
│       ├───ArmProcessor  
│       │   ├───ARM  
│       └───ProductConfiguration    
│           ├───Abstractions  
│           │   ├───ARM  
│           │   │   ├───ArchitectureVersions  
│           │   │   ├───ImageAnnotations  
│           │   │   └───Operators  
│           │   └───LLVM  
│           ├───Models  
├───DebugTime  _\<\< \<\< \<\< \<\< \<\< \<\< \<\< Zelig Debugger_  
│   ├───ArmProcessorEmulation  
│   │   ├───Forms  
│   │   ├───Hosting  
│   │   ├───Models  
│   │   │   ├───Chipset  
│   │   │   ├───Display  
│   │   │   ├───FlashMemory  
│   │   │   └───VoxSoloFormFactor  
│   │   └───Properties  
│   ├───Debugger  
│   │   ├───Execution  
│   │   │   └───ValueHandles  
│   │   ├───Forms  
│   │   ├───UserControls  
│   │   └───VisualTree  
│   │       ├───VisualEffects  
│   │       └───VisualItems  
│   └───Loader  
├───Documents  
│   ├───ExternalSpecs  
│   ├───Internal  
│   └───Papers  
├───External  
│   └───Binaries  
│       ├───binutils  
│       ├───CSharpParser  
│       ├───GLEE  
│       ├───JTAG  
│       └───readelf  
├───RunTime   _\<\< \<\< \<\< \<\< \<\< \<\< \<\< Zelig Runtime_  
│   ├───DeviceModels  
│   │   ├───... 
│   │   ├───LLVM  
│   │   │   ├───HardwareModel  
│   │   │   │   └───Drivers  
│   │   │   ├───Properties  
│   │   │   └───SystemServices  
│   │   ├───...  
│   │   ├───Perf  
│   │   │   ├───Dhrystone  
│   │   │   ├───v8  
│   │   │   └───Whetstone  
│   │   ├───QuickTest  
│   │   ├───TestMethodGen  
│   │   ├───...   
│   ├───Framework   
│   │   ├───mscorlib   
│   │   ├───mscorlib_UnitTest   
│   │   ├───system   
│   │   └───System_Core   
│   └───Zelig   
│       ├───Common   
│       │   ├───Attributes   
│       │   │   ├───CallQualifiers   
│       │   │   ├───CompileTimeOptions   
│       │   │   ├───HardwareModeling   
│       │   │   ├───OpenClasses   
│       │   │   └───TypeSystem   
│       │   ├───Collections   
│       │   ├───Configuration   
│       │   │   ├───Attributes   
│       │   │   ├───Categories   
│       │   │   └───Interfaces   
│       │   ├───Debugging   
│       │   ├───Exceptions   
│       │   ├───Helpers   
│       │   ├───PerformanceCounters   
│       ├───CommonPC   
│       ├───Kernel   
│       │   ├───FrameworkOverrides   
│       │   │   ├───Diagnostics   
│       │   │   ├───Globalization   
│       │   │   ├───Reflection   
│       │   │   ├───Resources   
│       │   │   ├───Runtime   
│       │   │   │   ├───CompilerServices   
│       │   │   │   └───InteropServices   
│       │   │   └───Threading   
│       │   ├───FrameworkOverrides_System   
│       │   │   └───IO   
│       │   │       └───Ports   
│       │   ├───GarbageCollectors   
│       │   ├───HardwareModel   
│       │   │   └───TargetPlatform   
│       │   │       ├───ARMv4   
│       │   │       ├───ARMv5   
│       │   │       ├───ARMv5_VFP   
│       │   │       └───LLVM   
│       │   ├───Helpers   
│       │   ├───ManagedHeap   
│       │   ├───MemoryManagers   
│       │   ├───Properties   
│       │   ├───SmartHandles   
│       │   ├───Support   
│       │   ├───Synchronization   
│       │   ├───SystemServices   
│       │   └───TypeSystemManagers   
│       └───TypeSystem   
│           ├───Environment   
│           ├───Fields   
│           ├───Methods   
│           └───Types   
├───Scripts   
└───Test   
    ├───ArmEmulator
    ├───GenericInstantiationClosure   
    │   ├───Fail_Field   
    │   ├───Fail_GenericMethod1   
    │   ├───Fail_GenericMethod2   
    │   ├───Fail_Inheritance   
    │   ├───Fail_Method   
    │   └───Pass   
    ├───HelloWorld   
    ├───il2n_Tests   
    ├───LLVM   
    ├───mbed   
    └───Scripts  