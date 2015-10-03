SPI Implementation
======================

## Rules for future peripherals:
**In order to properly follow the established pattern for adding more devices, a certain set of rules must be followed.**  
  
1. Code not necessary for the actual implementation of the UWP interface must stay in UWP (i.e. the kernel does not need lists)  
2. The kernel should only be used for connecting UWP with the correct device implementation, and initializing the device  
3. The hardware provider should be stupid. It is essentially a glorified getter  
4. The device implementation must be the *correct* implementation of the peripheral, regardless of what UWP does  
5. Board configurations should be very minimal, lightweight, and easy to understand (they will be created/modified by users)  

## Board Chip Select configuration:
**The user has 3 options for configuring the chip select pin for a given SPI port**  
  
1. Default CS: Pass the CS from board config to UWP SpiConnectionSettings, and the default (mbed toggled) CS pin will be used  
2. Non-default CS: Pass any CS pin to SpiConnectionSettings, and that pin will be toggled via the GPIO controller  
3. Manual CS: Change the CS pin in board config to NC and pass NC to SpiConnectionSettings. User will be responsible for toggling pin via GPIO controller  
  

## UWP:
**The UWP section contains everything needed for UWP compliance.**  
  
In order to be UWP compliant, we need to investigate what the objects look like in that namespace. A good tool for that is ILSpy - simply find
the .winmd file that contains your namespace, and you will be able to view the public headers, along with comments. Copy all files that are not 
grayed out and add them under the appropriate folder under Runtime/Framework/. Once you do this, and start implementation, you will realize that 
you need calls to the system to actually make things work (such as writing to SPI). To address this, UWP should contain an abstract class of
what calls it needs (write, read, etc.) and the Kernel needs to provide the implementation of this interface.

## Kernel:
**The kernel portion acts as an adapter between UWP and the proper implementation of your device** 
   
The keyword there is "proper". Nothing in kernel should have unnecessary objects that the UWP framework requires. Example: UWP needs a list of 
SPI devices on the system. The kernel should NOT create and return a list for UWP, but rather return a delimited string and have the UWP 
implementation deal with splitting it and creating a list. The implementation of the kernel code should also be as small as possible, and only 
handle connecting UWP to the device implementation, and initializing the device.
  
In order to call kernel code from UWP, mark private methods in UWP as extern, add the attribute [MethodImpl( MethodImplOptions.InternalCall )] 
and implement those methods as public in the kernel (make sure that the names/headers of the functions are the same!)  
  
So now we have a kernel that provides device implementation to UWP, but how do we get this "implementation" and where does it come from? The answer 
to this is the HardwareProvider  

## Hardware Provider:
**The Hardware Provider is a singleton that is responsible for pin reservation and returning hardware information (such as pin numbers) to the kernel**  
  
The Hardware Provider should be kept stupid. It is nothing more than a mechanism for getting board specific information to the kernel. At the kernel
level, the Hardware Provider is an abstract class that only handles pin reservation. The other portion of the Hardware Provider is implemented 
in CortexMOnMBED\HardwareModel. The reason this portion is necessary is so that it can get board specific settings and mbed specific ones. The main 
thing the second part of Hardware Provider is responsible for is providing the correct device implementation object.

## Device Implementation:
**The device implementation is responsible for the platform specific implementation of the abstract class added in the UWP**  
  
The device implementation is just what it claims to be: the implementation of a device interface. This implementation contains all of the mbed 
(or CMSIS/other) dependent code. This implementation should be the correct one (read: the ideal implementation of a low-level device). This means 
that it should be efficient, and correct code that can cover all of the different device configurations. Example: the mbed api has no notion of 
Tsetup and Thold for SPI, but the actual device implementation needs to account for these, and ensure that they are upheld.

## Board Configuration:
**The board configuration contains all board specific information, such as the list of SPI ports and their pins, and a mapping of pin numbers to the index in the pin reservation table**  
  
The board configuration files (currently LPC1768 and K64F) are used for board specific settings. The Hardware Provider at the CortexMOnMBED level 
has access the the Board abstract singleton, which, similar to the Hardware Provider, gets its implementation at compile time. In order to specify 
a different board, a user must edit the -Reference tag in the mbed_config file, and reference the correct assembly. All board specific settings 
must be accessed through the hardware provider which should get them from Board.Instance in order to follow the established pattern.
