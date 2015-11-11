# System Architecture

Zelig is not dissimilar from LLVM. As a matter of facts, the code base started a while back and the vast majority of the code was created by D.M., the same developer that created the core of .NET Micro Framework Type System (TS) interpreter.  
Zelig ingests MSIL out of a standard .dll or .exe set of files, and then transforms MSIL into a custom high level IR to apply a series of optimizations, just like LLVM does. Zelig also features an abstraction for the target HW platform, which is used to produce a runnable image. 

Code in intermediate representation (IR) is represented as a Control Flow Graph (CFG) of basic blocks describes as rich set of Expression and Operator types that map well to MSIL constructs. Types are represented by a rich hierarchy of Type Representation that maps to the well known CLI topology. 
The complete [IR class diagram](https://github.com/NETMF/llilum-pr/wiki/IR_ClassDiagram.jpg) gives you an idea of the IR organization. 

During the optimization phases the CFG is traversed by a set of Transformation Handlers; there expression, operators, and types are morphed and transformed to adapt to application and platform requirements. 
The IR is lowered twice and ARM code is emitted for ARMv4 and v5 ISA directly, and a runnable imaged is synthesized based on the platform abstraction implemented by the user. For ARMv7 high level IR is translated into equivalent LLVM bit code, optimized and compiled through LLVM standard tools, such as [opt](http://llvm.org/docs/CommandGuide/opt.html) and [llc](http://llvm.org/docs/CommandGuide/llc.html). The resulting object code can be linked just like any other object code. 

In our [demo](https://github.com/NETMF/llilum-pr/wiki/demo) we use [GCC for ARM](https://launchpad.net/gcc-arm-embedded/) and [Mbed](https://mbed.org/) to target a [NXP LPC1768](https://developer.mbed.org/platforms/mbed-LPC1768/) processor. In general, anything that applies to the LPC1768 processor applies to all other supported boards as well. Given the uniformity of the code base, you will be able to just substitute the string 'LPC1768' with, say, 'K64F' and instructions will just apply verbatim.


## Type System Representation
Let's look into some details of the TS IR. This is where all code transformation happen. 
Mostly the TS representation is made of a CFG of Expressions and Operators created during the MSIL ingestion. 
At that time, Generics are resolved as well into a flat representation on the internal type hierarchy. 
The same Type Representation is used both at Compile -Time and Run -Time. This characteristic is peculiar to Zelig and makes the system elegant and self-contained. The Type System is part of the run-time support in the code base.

A type is represented as an instance of [TypeRepresentation](https://github.com/NETMF/llilum-pr/blob/il2ir_demo/zelig/Zelig/RunTime/Zelig/TypeSystem/Types/TypeRepresentation.cs): 


    public abstract class TypeRepresentation : BaseRepresentation
    {
        [Flags]
        public enum Attributes
        {
            VisibilityMask     = 0x00000007,
            NotPublic          = 0x00000000, // Class is not public scope.
            Public             = 0x00000001, // Class is public scope.
            NestedPublic       = 0x00000002, // Class is nested with public visibility.
            NestedPrivate      = 0x00000003, // Class is nested with private visibility.
            NestedFamily       = 0x00000004, // Class is nested with family visibility.
            NestedAssembly     = 0x00000005, // Class is nested with assembly visibility.
            NestedFamANDAssem  = 0x00000006, // Class is nested with family and assembly visibility.
            NestedFamORAssem   = 0x00000007, // Class is nested with family or assembly visibility.

            // Use this mask to retrieve class layout informaiton
            // 0 is AutoLayout, 0x2 is SequentialLayout, 4 is ExplicitLayout
            LayoutMask         = 0x00000018,
            AutoLayout         = 0x00000000, // Class fields are auto-laid out
            SequentialLayout   = 0x00000008, // Class fields are laid out sequentially
            ExplicitLayout     = 0x00000010, // Layout is supplied explicitly
            // end layout mask

            // Use this mask to distinguish a type declaration as a Class, ValueType or Interface
            ClassSemanticsMask = 0x00000020,
            Class              = 0x00000000, // Type is a class.
            Interface          = 0x00000020, // Type is an interface.

            // Special semantics in addition to class semantics.
            Abstract           = 0x00000080, // Class is abstract
            Sealed             = 0x00000100, // Class is concrete and may not be extended
            SpecialName        = 0x00000400, // Class name is special.  Name describes how.

            // Implementation attributes.
            Import             = 0x00001000, // Class / interface is imported
            Serializable       = 0x00002000, // The class is Serializable.

            // Use tdStringFormatMask to retrieve string information for native interop
            StringFormatMask   = 0x00030000,
            AnsiClass          = 0x00000000, // LPTSTR is interpreted as ANSI in this class
            UnicodeClass       = 0x00010000, // LPTSTR is interpreted as UNICODE
            AutoClass          = 0x00020000, // LPTSTR is interpreted automatically
            CustomFormatClass  = 0x00030000, // A non-standard encoding specified by CustomFormatMask
            CustomFormatMask   = 0x00C00000, // Non-standard encoding information for native interop.
            // end string format mask

            BeforeFieldInit    = 0x00100000, // Initialize the class any time before first static field access.

            // Flags reserved for runtime use.
            ReservedMask       = 0x00040800,
            RTSpecialName      = 0x00000800, // Runtime should check name encoding.
            HasSecurity        = 0x00040000, // Class has security associate with it.

            //--//

            None               = 0x00000000,
        }

        //
        // This is just a copy of Microsoft.Zelig.MetaData.ElementTypes
        //
        public enum BuiltInTypes : byte
        {
            END         = 0x00,
            VOID        = 0x01,
            BOOLEAN     = 0x02,
            CHAR        = 0x03,
            I1          = 0x04,
            U1          = 0x05,
            I2          = 0x06,
            U2          = 0x07,
            I4          = 0x08,
            U4          = 0x09,
            I8          = 0x0A,
            U8          = 0x0B,
            R4          = 0x0C,
            R8          = 0x0D,
            STRING      = 0x0E,
            ///////////////////////// every type above PTR will be simple type
            PTR         = 0x0F,    // PTR <type>
            BYREF       = 0x10,    // BYREF <type>
            ///////////////////////// Please use VALUETYPE. VALUECLASS is deprecated.
            VALUETYPE   = 0x11,    // VALUETYPE <class Token>
            CLASS       = 0x12,    // CLASS <class Token>
            VAR         = 0x13,    // a class type variable VAR <U1>
            ARRAY       = 0x14,    // MDARRAY <type> <rank> <bcount> <bound1> ... <lbcount> <lb1> ...
            GENERICINST = 0x15,    // instantiated type
            TYPEDBYREF  = 0x16,    // This is a simple type.
            I           = 0x18,    // native integer size
            U           = 0x19,    // native unsigned integer size
            FNPTR       = 0x1B,    // FNPTR <complete sig for the function including calling convention>
            OBJECT      = 0x1C,    // Shortcut for System.Object
            SZARRAY     = 0x1D,    // Shortcut for single dimension zero lower bound array
            ///////////////////////// SZARRAY <type>
            ///////////////////////// This is only for binding
            MVAR        = 0x1E,    // a method type variable MVAR <U1>
            CMOD_REQD   = 0x1F,    // required C modifier : E_T_CMOD_REQD <mdTypeRef/mdTypeDef>
            CMOD_OPT    = 0x20,    // optional C modifier : E_T_CMOD_OPT <mdTypeRef/mdTypeDef>
            ///////////////////////// This is for signatures generated internally 
            INTERNAL    = 0x21,    // INTERNAL <typehandle>
            ///////////////////////// Note that this is the max of base type excluding modifiers
            MAX         = 0x22,    // first invalid element type
            MODIFIER    = 0x40,
            SENTINEL    = 0x01 | MODIFIER, // sentinel for varargs
            PINNED      = 0x05 | MODIFIER
        }

        [Flags]
        public enum BuildTimeAttributes : uint
        {
            ForceDevirtualization = 0x00000001,
            ImplicitInstance      = 0x00000002,
            NoVTable              = 0x00000004, // The class does not have a vtable.
            FlagsAttribute        = 0x00000010,
        }

        public enum InstantiationFlavor
        {
            Delayed  ,
            Class    ,
            ValueType,
        }

        public sealed class GenericContext
        {
            //
            // State
            //

            private TypeRepresentation           m_template;
            private TypeRepresentation[]         m_parameters;
            private GenericParameterDefinition[] m_parametersDefinition;

            //
            // More ...
            //

            
        }

        public struct InterfaceMap
        {
            public static readonly InterfaceMap[] SharedEmptyArray = new InterfaceMap[0];

            //
            // State
            //

            public InterfaceTypeRepresentation Interface;
            public MethodRepresentation[]      Methods;
        }

        public static readonly TypeRepresentation[] SharedEmptyArray = new TypeRepresentation[0];

        //
        // State
        //

        protected AssemblyRepresentation          m_owner;
        protected BuiltInTypes                    m_builtinType;
        protected Attributes                      m_flags;
        protected BuildTimeAttributes             m_buildFlags;
        protected string                          m_name;
        protected string                          m_namespace;
        protected TypeRepresentation              m_enclosingClass;

        protected GenericContext                  m_genericContext;

        protected TypeRepresentation              m_extends;
        protected InterfaceTypeRepresentation[]   m_interfaces;
        protected FieldRepresentation[]           m_fields;
        protected MethodRepresentation[]          m_methods;
        protected MethodImplRepresentation[]      m_methodImpls;

        //
        // These fields are synthesized from the analysis of the type hierarchy and the type characteristics.
        //
        [WellKnownField( "TypeRepresentation_InterfaceMethodTables" )] protected InterfaceMap[]         m_interfaceMethodTables;
        [WellKnownField( "TypeRepresentation_MethodTable" )]           protected MethodRepresentation[] m_methodTable;
        [WellKnownField( "TypeRepresentation_VTable" )]                protected VTable                 m_vTable;

        //
        // More...
        //

    }


### Type Hierarchy 
In the picture below the type hierarchy for .NET types: 

![.NET Types](https://github.com/NETMF/llilum-pr/wiki/dotNETTypes.png)

This is the equivalent Zelig types hierarchy, with the addition of the delayed type representation for generics parameters: 

![Zelig types](https://github.com/NETMF/llilum-pr/wiki/ZeligTypes.png)

### LLVM Type System layout
LLVM is built for languages like C++. C++ lays virtual tables in front of an object and so will we in our translation to LLVM bit code. 

Any reference type will derive from `System.Object`. `System.Object` will carry the `Microsoft.Zelig.Runtime.ObjectHeader` member at offset 0, to allows handling of the Object in the managed heap. The `ObjectHeader` type will carry the identity and GC tracking info. Any reference to a managed object will be represented in LLVM as a native pointer to the object payload, i.e. will be a pointer pointing just past the object header. 

In Zelig code this looks like: 

`namespace Microsoft.Zelig.Runtime`  
`{`  
    `public class ObjectHeader`  
    `{`  
        `[TS.WellKnownField( "ObjectHeader_MultiUseWord" )] public volatile int       MultiUseWord;`  
        `[TS.WellKnownField( "ObjectHeader_VirtualTable" )] public          TS.VTable VirtualTable;`  

        `// more ...` 
    `}`   
`}`   

and the ObjectHeader is prepended to all Objects and Boxed types. 


In LLVM bit code this looks like: 

**ReferenceType and BoxedValue**  
`%T = type <{ %System.Object, %t }>`  

where 

`%System.Object = type <{ %Microsoft.Zelig.Runtime.ObjectHeader }>`  


**ValueType**  
`%V = type <{ %v }>`   

e.g.:   
`%System.IntPtr = type <{ i8* }>`    



## Basic Blocks
Basic Blocks are created during the ingestion of MSIL. Here is the class diagram for basic blocks:


![Basic Blocks Class Diagram in Zelig IR](https://github.com/NETMF/llilum-pr/wiki/BasicBlocks.jpg)


## Expressions 
Expressions are created during the ingestion of MSIL. 

![Expressions Class Diagram in Zelig IR](https://github.com/NETMF/llilum-pr/wiki/Expressions.jpg)


## Operators 
Operators are the interesting part of the system. Operators are created during the ingestion of MSIL and then evolved, or substituted, during the optimization phase. 
A subset of the operators hierarchy is in the picture below.  

![A Small Subset of Operators Class Diagram in Zelig IR](https://github.com/NETMF/llilum-pr/wiki/OperatorsSubset.jpg)

#Code Transformations
Code transformation reflect on operators and expressions. Expressions are evaluated and transformed, Operators are evolved. Let's see one example of an operator transformation:  


        [CompilationSteps.PhaseFilter( typeof(Phases.MidLevelToLowLevelConversion) )]
        [CompilationSteps.OperatorHandler( typeof(FieldOperator) )]
        private static void Handle_FieldOperator( PhaseExecution.NotificationContext nc )
        {
            FieldOperator       op          = (FieldOperator)nc.CurrentOperator;
            FieldRepresentation fd          =                op.Field;
            Debugging.DebugInfo debugInfo   =                op.DebugInfo;
            bool                fIsVolatile =                (fd.Flags & FieldRepresentation.Attributes.IsVolatile) != 0;
            Operator            opNew;

            CHECKS.ASSERT( op.MayThrow == false, "Unexpected throwing operator: {0}", op );

            if(op is LoadInstanceFieldAddressOperator)
            {
                opNew = BinaryOperator.New( debugInfo, BinaryOperator.ALU.ADD, false, false, op.FirstResult, op.FirstArgument, nc.TypeSystem.CreateConstant( fd.Offset ) );
            }
            else if(op is LoadInstanceFieldOperator)
            {
                opNew = LoadIndirectOperator.New( debugInfo, fd.FieldType, op.FirstResult, op.FirstArgument, new FieldRepresentation[] { fd }, fd.Offset, fIsVolatile, false );
            }
            else if(op is StoreInstanceFieldOperator)
            {
                opNew = StoreIndirectOperator.New( debugInfo, fd.FieldType, op.FirstArgument, op.SecondArgument, new FieldRepresentation[] { fd }, fd.Offset, false );
            }
            else
            {
                throw TypeConsistencyErrorException.Create( "Unexpected operator {0} at phase {1}", op, nc.Phase );
            }

            op.SubstituteWithOperator( opNew, Operator.SubstitutionFlags.CopyAnnotations );

            nc.MarkAsModified();
        }

In the example above the code is lowering mid-level IR and it transforming a `LoadInstanceFieldAddress` operator into a simple offset by representing it with a `BinaryOperator` for a _add_. This is applied, for example, when array elements are accessed through managed pointer types by CLI standard. 
The Operator is then marked as modified so that the transformation pipeline can continue. 
The annotations on the method ensure that this transformation is applied to the right operator at the correct operator during the proper compilation phase. 

Another interesting example below shows how the `ObjectAllocationOperation`, which is created for any '_newobj_' MSIL instruction in the IL generated by the C# compiler, is transformed into a simple function call to a well-known method the MTEE and TS use to allocate objects out of the managed heap. `Objects`, `Arrays`, `Strings`, etc..., all get a specific treatments that ensures type safety and performance optimization even before actual machine code is generated. 

Notice how operations such as getting the length of an array or calling a finalizer are injected by transforming the original operator. 


        [CompilationSteps.PhaseFilter( typeof(Phases.HighLevelTransformations) )]
        [CompilationSteps.OperatorHandler( typeof(ObjectAllocationOperator) )]
        private static void Handle_ObjectAllocationOperator( PhaseExecution.NotificationContext nc )
        {
            ObjectAllocationOperator        op               = (ObjectAllocationOperator)nc.CurrentOperator;
            TypeSystemForCodeTransformation ts               = nc.TypeSystem;
            WellKnownMethods                wkm              = ts.WellKnownMethods;
            MethodRepresentation            mdAllocateObject = wkm.TypeSystemManager_AllocateObject;
            MethodRepresentation            md               = op.Method;
            TypeRepresentation              td               = op.Type;
            CallOperator                    call;
            Expression[]                    rhs;
            Expression                      exStringLen      = null;

            //
            // Is this a special object?
            //
            for(var td2 = td; td2 != null; td2 = td2.Extends)
            {
                if(ts.GarbageCollectionExtensions.ContainsKey( td2 ))
                {
                    mdAllocateObject = wkm.TypeSystemManager_AllocateObjectWithExtensions;
                    break;
                }
            }

            if(md != null)
            {
                if(md == wkm.StringImpl_ctor_char_int)
                {
                    exStringLen = op.SecondArgument;
                }
                else if(md == wkm.StringImpl_ctor_charArray)
                {
                    TemporaryVariableExpression tmp = nc.CurrentCFG.AllocateTemporary( ts.WellKnownTypes.System_Int32, null );

                    op.AddOperatorBefore( ArrayLengthOperator.New( op.DebugInfo, tmp, op.FirstArgument ) );

                    exStringLen = tmp;
                }
                else if(md == wkm.StringImpl_ctor_charArray_int_int)
                {
                    exStringLen = op.ThirdArgument;
                }
            }

            //
            // Special case to allocate strings built out of characters.
            //
            if(exStringLen != null)
            {
                mdAllocateObject = wkm.StringImpl_FastAllocateString;

                rhs  = ts.AddTypePointerToArgumentsOfStaticMethod( mdAllocateObject, exStringLen );
                call = StaticCallOperator.New( op.DebugInfo, CallOperator.CallKind.Direct, mdAllocateObject, op.Results, rhs );
                op.SubstituteWithOperator( call, Operator.SubstitutionFlags.CopyAnnotations );
                call.AddAnnotation( NotNullAnnotation.Create( ts ) );

                nc.MarkAsModified();
            }
            else
            {
                TemporaryVariableExpression typeSystemManagerEx = FetchTypeSystemManager( nc, op );

                rhs  = new Expression[] { typeSystemManagerEx, ts.GetVTable( op.Type ) };
                call = InstanceCallOperator.New( op.DebugInfo, CallOperator.CallKind.Virtual, mdAllocateObject, op.Results, rhs, true );
                op.SubstituteWithOperator( call, Operator.SubstitutionFlags.CopyAnnotations );
                call.AddAnnotation( NotNullAnnotation.Create( ts ) );

                var mdFinalizer = td.FindDestructor();
                if(mdFinalizer != null && mdFinalizer.OwnerType != ts.WellKnownTypes.System_Object)
                {
                    var mdHelper   = wkm.Finalizer_Allocate;
                    var rhsHelper  = ts.AddTypePointerToArgumentsOfStaticMethod( mdHelper, call.FirstResult );
                    var callHelper = StaticCallOperator.New( call.DebugInfo, CallOperator.CallKind.Direct, mdHelper, rhsHelper );

                    call.AddOperatorAfter( callHelper );
                }

                nc.MarkAsModified();
            }
        }


[==> next](https://github.com/NETMF/llilum/wiki/system.build)