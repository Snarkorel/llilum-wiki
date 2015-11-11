LLILUM uses the Itanium zero-cost exception handling model detailed [here](http://mentorembedded.github.io/cxx-abi/abi-eh.html). Though much of this article will focus on ARM architectures, the concepts described here apply equally to other architectures.

This article also assumes basic familiarity with LLVM's exception-handling instructions. Please reference the landingpad, invoke, and resume instructions in the [LLVM language reference manual](http://llvm.org/docs/LangRef.html). More information on landing pads can also be found [here](http://llvm.org/docs/ExceptionHandling.html).

#Compile-time
Exception handlers are defined in MSIL by protected regions. In LLILUM, we transform these regions into landing pads that LLVM can recognize, and use VTable pointers as the handler type. This can be done in any phase during or after the TransformFinallyBlocksIntoTryBlocks phase.

###Flow Transformation
Before transformation, each block references a list of handler blocks that it's protected by.  Our goal is to transform each unique list of handler blocks into a single landing pad block.

Each handler is represented by a header block of type ExceptionHandlerBasicBlock which contains a single unconditional branch instruction to the entry block of the handler. For any given set of handlers we begin by creating an ordered list of the handler types as VTable pointers, as well as the entry block for each type. Catch-all and finally blocks are represented with the System.Object type and a null VTable pointer, respectively. Exception spec (filter) blocks are ignored.

Once we have the handler list, we create the following new blocks as injection points: a new exception block and a resume block. The exception block contains a landing pad with a catch clause for each type. The result of this operator is a pair containing a pointer to the opaque (native) exception struct, and an integer selector value. The landing pad operator is followed by a switch on the extracted selector value, which maps one-based handler indices to their respective blocks.  Zero and other unhandled values jump to the Resume block, which continues unwinding.

Finally, the protected block is redirected to the new exception block and the original header blocks are removed via dead stripping.

##LLVM Codegen
In the final stage, landing-pad and resume instructions are translated into their LLVM counterparts one-to-one. While processing each call operator, we determine whether the current block is protected by a landing pad. If so, we split the block at the operator and replace it with an invoke instruction. Additionally, we ensure that a predefined personality function `LLOS_Personality` is associated with the calling function.

#Runtime Unwinding
At runtime, the unwinding process (throwing an exception) is begun by creating an exception object via `LLOS_AllocateException` and passing the resulting opaque pointer to `LLOS_Unwind_RaiseException`. These two functions are roughly analogous to the C++ functions `__cxx_allocate_exception` and `__cxa_throw`.  and will ultimately result in a jump to an appropriate landing pad or a call to abort() if none can be found.  On mbed devices, this generally results in a trap which flashes pre-defined LED patterns.

`LLOS_Unwind_RaiseException` uses a provided unwind library (e.g. GNU's libunwind on ARM) to walk the call stack and inspect each frame for potential handlers in three phases. In each phase, the unwinder calls each frame's personality function (defined at compile time). The personality function is passed a pointer to the exception and an opaque pointer to the execution context, through which we can retrieve pointers to the containing function and the exception table data.

Phases:  
1. Search phase: Search for potential handlers and return either `HandlerFound` or `ContinueUnwind`. No state is modified during this phase.  
2. Handler phase: Repeat the search in phase one and set registers for entry into the landing pad, returning `InstallContext`. This phase must stop at the same frame as the search phase.  
3. Cleanup phase: Execute each cleanup block leading up to the landing pad found in the handler phase. Note: Since LLILUM doesn't emit cleanup blocks, this is a no-op.  

See `Microsoft.Zelig.Runtime.Unwind` for more details on the specific mechanics of unwinding. More information on exception table layout can be found [here](http://mentorembedded.github.io/cxx-abi/exceptions.pdf).