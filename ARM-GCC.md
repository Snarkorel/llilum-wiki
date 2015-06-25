Installer/binaries can be found at: https://launchpad.net/gcc-arm-embedded

The compiler tools are prepended with gcc-arm-none-eabi. This is the version of GCC that compiles for naked/no-OS targets.

The ELF images are all combined together with gcc-arm-none-eabi-ld. This file is then linked with: 

  external\mbed\libraries\mbed\targets\cmsis\TARGET_NXP\TARGET_LPC176X\TOOLCHAIN_GCC_ARM\startup_LPC17xx.s
    (configures heap and stack memory regions and configures the interrupt table)

  external\mbed\libraries\mbed\targets\cmsis\TARGET_NXP\TARGET_LPC176X\TOOLCHAIN_GCC_ARM\LPC1768.ld
    (a linker script that specifies how the image sections are ordered)

Then gcc-arm-none-eabi-objcopy is used to convert the executable ELF image into a .bin file. I think this does the image load and globals initialization and then writes out the image data.