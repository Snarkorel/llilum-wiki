Configuration and usage of arm-none-eabi-gdb from the GCC4MBED project in Windows:

1 Follow the steps in https://developer.mbed.org/handbook/Windows-serial-configuration to setup Windows for serial communication with the MBED

2 The MRI stub needs a relatively updated firmware. Update your mbed with the current firmware following the steps at : https://developer.mbed.org/handbook/Firmware-LPC1768-LPC11U24

3 Assuming your env is correctly set up, running build.bat will build an image in debug mode. Deploy the image to the mbed copying it, and push the button to restart it. At restart, the program with the mri stub will automatically halt the mbed, and wait for a gdb remote conection.

4 Again, assuming your env is correctly set, you can run debug.bat in the same folder to begin a gdb remote session. You might need to edit the debug.bat file to pass the correct COM port where your mbed connection is. This is easily checkable in the windows device manager, under Ports, there you should find an "mbed serial port (COMX)" where COMX is set to the currently used com port. Replace that info in the gdb call inside the batch file.