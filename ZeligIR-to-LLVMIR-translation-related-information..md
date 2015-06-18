## Native Int Size.
Being decoupled from mid-stage and the backend, LLVM has no way of knowing the maximum size for a native integer on the IR level. Zelig needs this information to accurately represent the CLI "Native-Size Integer" type.

We are going to use a NativeIntSize setting for Zelig to specify in bits the size of the native size, to be changed before compilation depending of the desired final target.

On the other side, Zelig is already taking that information from the current ARM implementation and assumes it's 32 bits. It would be nice to set this without having to change too much of the original codebase.

## Leaner Platform Abstraction.
The correct way to use Zelig would be to write a platform abstraction. However, this includes defining too much low level settings that LLVM needs to keep generic. It would be great to modify the platform abstraction class to only include the information that is really needed until the point we insert our llvm phase so we can use it to set it up with settings relative to the final compilation target (e.g, the previous Native Ints Size)