## Setup

In a developer prompt run:
* **external\Install_LLVM.bat**   (will take ~1.5 hours)
* **external\Install_gcc4mbed.bat** (will take ~15min)

Define environment variables LLVM_INCLUDE and LLVM_LIBS to point to include files and libraries for LLVM. LLVM_LIBS should not include the flavor of the bits (Debug or Release). 

## Build the demo project

To build and run the LLVMIR generator, invoke the LLVM compiler to produce a .o file, and link this against the LLVMHost project run **buildDemo.bat**. If your demo board is at e:\, the compiled binary will end up there.

## Demo board circuit description

Changes an RGB LED color in response to distance measured by an optical distance sensor.

Hardware setup as follows:

1. mbed NXP LPC1768 demo board
2. Sharp 2Y0A21 optical position sensor 
   * Power tied to +5V (mbed pin 39), and Ground (mbed pin 1)
   * 0.1 uf capictor between power pins of sensor (reduces power draw spikes)
   * 0.1 uf capictor between Ground pin and analog output pin (reduces output noise)
   * analog output tied directly to pin 20 of mbed board

3. RGB common cathode LED
   * R, G + B tied to mbed board pins 21, 22 and 23 respectively via 180 ohm resistors
   * Common cathode tied to Ground (pin 1)
