# Build System 
Zelig build system is made of phase drivers, phases, filter for phases, transformations, operator handler and image builders. Each phase subclasses the `PhaseDriver` abstract class and is executed in a specific order, determined by the usage of the `PhaseOrdering` attribute. 
The build system is implemented in the [_CodeTransformation_](https://github.com/NETMF/llilum/tree/il2ir_demo/zelig/Zelig/CompileTime/CodeGenerator/CodeTransformation) project. 
The entry point for the build system is the `Controller` class, who executes phase-by-phase and drivers the usage of the handlers through phases. 

## Phase Drivers
Phase drivers are utility classes, that implement some infrastructure for the optimization engine. Available phase drivers are:

* `CallGraph`: tracks closure and static constructor dependencies. 
* `CallsDatabase`: provides support for in-lining and inspecting calls to and from a method.
* `ImplementExternaMethods': provides support to implement external methods, e.g. methods that can call into existing C/C++ code. 
* `ImplementInternaMethods': provides support to implement internal methods. e.g. methods in the framework core such as `Object.MemberwiseClone` or `Array.Length`. 
* `ParallelTransformationsHandler`: provides support to enumerate and transform instances of methods and CFGs in parallel.
* `DelegationCache`: provides support to hook phase handlers to a phase.  

## Phases
Available phases are, in the order:

1. ReduceNumberOfTemporaries
2. TransformFinallyBlocksIntoTryBlocks
3. ApplyClassExtensions
4. PrepareImplementationOfInternalMethods  
5. CrossReferenceTypeSystem
6. ApplyConfigurationSettings
7. ResourceManagerOptimizations
8. HighLevelTransformations
9. PropagateCompilationConstraints
10. ComputeCallsClosure
11. EstimateTypeSystemReduction
12. CompleteImplementationOfInternalMethods
13. ReduceTypeSystem
14. PrepareExternalMethods
15. DetectNonImplementedInternalCalls
16. OrderStaticConstructors
17. LayoutTypes
18. HighLevelToMidLevelConversion
19. FromImplicitToExplictExceptions
20. MidLevelToLowLevelConversion
21. ConvertUnsupportedOperatorsToMethodCalls
22. ExpandAggregateTypes
23. SplitComplexOperators
24. FuseOperators
25. Optimizations
26. ConvertToSSA
27. GenerateImage
28. PrepareForRegisterAllocation
29. CollectRegisterAllocationConstraints
30. AllocateRegisters
31. ConvertToLLVMIntermediateRepresentation
32. Done 

## Transformations 
Transformations are utility functions applied during to phases. Available transformations include:

* `InlineCall`
* `PerformClassExtension`
* `ReduceNumberOfTemporaries`
* `RemoveDeadCode`
* `RemoveSimpleIndirection`
* `SimplifyConditionCodeChecks`
* `SplitBasicBlocksAtExceptionSite`
* `TransformFinallyBLocksIntoTryBlocks` 

Below an example of a simply phase that uses a transformation through the `ParallelTranformationHandler`: 

    [PhaseOrdering( ExecuteAfter=typeof(ReduceNumberOfTemporaries) )]
    public sealed class TransformFinallyBlocksIntoTryBlocks : PhaseDriver
    {
        //
        // Constructor Methods
        //

        public TransformFinallyBlocksIntoTryBlocks( Controller context ) : base ( context )
        {
        }

        //
        // Helper Methods
        //

        public override PhaseDriver Run()
        {
            ParallelTransformationsHandler.EnumerateFlowGraphs( this.TypeSystem, delegate( ControlFlowGraphStateForCodeTransformation cfg )
            {
                Transformations.TransformFinallyBlocksIntoTryBlocks.Execute( cfg );

                Transformations.MergeExtendedBasicBlocks.Execute( cfg );
            } );

            return this.NextPhase;
        }
    }

## Operator Handler
Operator handles are assigned to phases by the `Controller` through the `DelegationCache`. 
A typical operator handler looks like the following:


    public class MethodTransformations
    {
        //--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//
        //--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//
        //--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//

        [CompilationSteps.PhaseFilter( typeof(Phases.HighLevelTransformations     ) )]
        [CompilationSteps.PhaseFilter( typeof(Phases.HighLevelToMidLevelConversion) )]
        [CompilationSteps.PhaseFilter( typeof(Phases.MidLevelToLowLevelConversion ) )]
        [CompilationSteps.PhaseFilter( typeof(Phases.ExpandAggregateTypes         ) )]
        [CompilationSteps.PostFlowGraphHandler()]
        private static void ProcessFlowGraphAfterTransformations( PhaseExecution.NotificationContext nc )
        {
            ControlFlowGraphStateForCodeTransformation cfg = nc.CurrentCFG;
            if(cfg != null)
            {
                if(Transformations.CommonMethodRedundancyElimination.Execute( cfg ))
                {
                    nc.StopScan();
                    return;
                }

                cfg.DropDeadVariables();
            }
        }

        //--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//
        //--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//
        //--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//--//

        [CompilationSteps.PreFlowGraphHandler()]
        private void InjectPrologueAndEpilogue( PhaseExecution.NotificationContext nc )
        {
            if(nc.Phase.ComparePositionTo< Phases.HighLevelTransformations >() >= 0 &&
               nc.Phase.ComparePositionTo< Phases.ReduceTypeSystem         >() <  0  )
            {
                ControlFlowGraphStateForCodeTransformation cfg = nc.CurrentCFG;
                if(cfg != null && cfg.SetProperty( "MethodWrappersAdded" ) == false)
                {
                    var wkm = nc.TypeSystem.WellKnownMethods;

                    {
                        var prologueStart = cfg.GetInjectionPoint( BasicBlock.Qualifier.PrologueStart );
                        var prologueEnd   = cfg.GetInjectionPoint( BasicBlock.Qualifier.PrologueEnd   );
    
                        AddCallToWrapper( nc, prologueStart, prologueEnd, wkm.Microsoft_Zelig_Runtime_AbstractMethodWrapper_Prologue, wkm.Microsoft_Zelig_Runtime_AbstractMethodWrapper_Prologue2 );
                    }

                    {
                        var epilogueStart = cfg.GetInjectionPoint( BasicBlock.Qualifier.EpilogueStart );
                        var epilogueEnd   = cfg.GetInjectionPoint( BasicBlock.Qualifier.EpilogueEnd   );

                        AddCallToWrapper( nc, epilogueStart, epilogueEnd, wkm.Microsoft_Zelig_Runtime_AbstractMethodWrapper_Epilogue, wkm.Microsoft_Zelig_Runtime_AbstractMethodWrapper_Epilogue2 );
                    }

                    nc.StartScan();
                }
            }
        }
    }

Please note the attribute that assign the handler usage to a specific phase. Also please note the usage of the `PreFlowGraphHandlerAttribute`: methods with this attribute get a chance to look at the ControlFlowGraphState before everyone else. 

[==> next](https://github.com/NETMF/llilum/wiki/LLILUM-Compiler-Frontend)
