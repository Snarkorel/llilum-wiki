Creating a Board Support Package
================================

Creating a Board Support Package can be a lengthy process, so please allocate several hours before starting. We would also like to
remind users that the board configurations are not yet finalized, and will most likely continue changing for quite some time.
While we will do our best to ensure all user-added board configurations can be built, we cannot guarantee that they will always work - that
will be up to the community.  
  
There are also plans to create a project template for creating new boards. Unfortunately, maintaining the template would be far too time consuming while the boards are in such a volatile state.
  
## Prerequisites
Before starting, please have the following:
* Llilum repo cloned on the machine
* mBed repo cloned on the machine
* Complete the [setup steps](https://github.com/NETMF/llilum/wiki/Setup) and **build the Zelig solution**
* (Optional) Complete the [SDK setup](https://github.com/NETMF/llilum/wiki/SDK-User-Guide) if adding BSP to SDK
  
## Preface
A large portion of this demo is copying the configuration of a similar board, so in order to reduce the use of words "new board name" and 
"similar board name", "STM32L152" will be used instead of "new board" and "LPC1768" will be used instead of "similar board". We chose these 
because they are both Cortex-M3 boards. For Cortex-M4, it would be best to base it on the K64F BSP.  
  
**NOTE:**  
**Please see [this commit](https://github.com/NETMF/llilum/commit/ca018e1f9c37b81f708f795a0b856ba65c69ef03) to see changes made for adding the STM32F401 or [this older commit](https://github.com/NETMF/llilum/commit/588b9190baad3ef1ab939670ffea2e440fd10830) to see all of the changes made to add the STM32L152 BSP**
  
## Creating the Board Configuration Projects
1. In Explorer, create a new folder called STM32L152 in `<repo root>\Zelig\BoardConfigurations\`
2. Open the BoardConfiguration solution under `<repo root>\Zelig\BoardConfigurations`
3. In Solution Explorer, add a new solution folder called "STM32L152"
4. Add a new "Class Library" project called "Board" to the STM32L152 solution folder and set its location to `<repo root>\Zelig\BoardConfigurations\STM32L152`
5. Repeat step 4, with a project called "Configuration"
6. In Solution Explorer, rename the Board project to "STM32L152" and the Configuration project to "STM32L152Configuration"
  
## Modifying the Board Configuration Projects
1. Unload the STM32L152, and LPC1768 projects (right click -> unload project)
2. From the LPC1768 csproj file, copy everything after the first "PropertyGroup" element, and replace the matching portions of the STM32L152 project
(meaning leave the first property group alone, and replace everything else)
3. Copy the "\<Import Project="...BuildEnv.props" .../>" line above the first "PropertyGroup" element, and replace the matching line in the STM32L152 csproj file
4. Unload the LPC1767Configuration and STM32L152Configuration projects
5. Copy the "OutputPath" elements from the LPC1768Configuration project to the STM32L152Configuration project
6. Replace the ItemGroup with DLL references in STM32L152Configuration project, with the one in LPC1768Configuration project. Repeat step 3 for this project.
7. Reload all of the projects
8. Open the properties for the STM32L152 project
9. Set Assembly Name to "STM32L152", Default Namespace to "Microsoft.Llilum.STM32L152", and Target Framework to ".NET 4.5"
10. Open the AssemplyInfo.cs file and set AssemblyTitle to "STM32L152" and AssemblyProduct to "STM32L152"
11. Open the properties for STM32L152Configuration project
12. **THE NEXT 2 STEPS ARE IMPORTANT TO GET RIGHT. DEBUGGING ISSUES WILL BE DIFFICULT IF NAMING CONVENTION NOT FOLLOWED**
13. Change Assembly Name to "Microsoft.Llilum.BoardConfigurations.STM32L152" and DefaultNamespace to "Microsoft.Llilum.BoardConfigurations"
14. Open the AssemblyInfo.cs file. Set AssemblyTitle and AssemblyProduct to "Microsoft.Llilum.BoardConfigurations.STM32L152"
  
## Adding Files to the Board Configuration Project
1. Add files called "Configuration.cs" and "STM32L152.FrontEndConfig" (text file) to the STM32L152Configuration project
2. Open the STM32L152.FrontEndConfig file and open the LPC1768.FrontEndConfig file from the LPC1768Configuration project
3. Copy the contents of the LPC1768.FrontEndConfig file into the STM32L152.FrontEndConfig file
4. In STM32L152.FrontEndConfig, change all instances of "LPC1768" to "STM32L152"
5. Follow steps 2-4 for the Configuration.cs file
6. Change the CoreClockFrequency to that of the STM32L152 (32000000 for the STM32L152 Nucleo Board). RealTimeClockFrequency will be 1MHz on mBed
7. This project should now be able to build

## Modify the Memory Map
1. Find a datasheet for your board such as this one: http://www.st.com/st-web-ui/static/active/en/resource/technical/document/datasheet/DM00115249.pdf
2. Determine the base memory address for Flash and RAM, and modify within the file
3. Change the class names of the memories to reflect the size accurately for the board
  
## Adding Files to the Board Project
1. In Explorer, open `<repo root>\Zelig\BoardConfigurations\STM32L152\Board` and `<repo root>\Zelig\BoardConfigurations\LPC1768\Board`
2. Copy everything from LPC1768 to STM32L152 except for the Properties folder and the .csproj file
3. You should be able to open all of the files in the STM32L152 project now
4. Go through all of the files, and change all instances of "LPC1768" to "STM32L152"
5. Open the mBed repo directory in explorer, and navigate to `<mbed repo>\libraries\mbed\targets\hal\TARGET_STM\TARGET_STM32L1`
6. Search for a file called "PinNames.h" 
7. Open the file, and copy the PinName enum into the GPIO.cs file PinName enum
8. This step is less trivial: find the file that contains the Interrupt Numbers. For STM32L152 it is `<mbed repo>\libraries\mbed\targets\cmsis\TARGET_STM\TARGET_STM32L1\TARGET_NUCLEO_L152RE\stm32l152xe.h`
9. Copy the IRQn_Type enum contents into the IRQn enum in NVIC.cs
10. Find the us_ticker.c file for the board being used (`<mbed root>\libraries\mbed\targets\hal\TARGET_STM\TARGET_STM32L1`), and determine what timer is being used (TIM5 in this case)
11. Set the IRQn value to the correct one for GetSystemTimerIRQNumber (TIM5_IRQn for STM32L152)
12. Find the PinToIndex function, and change it to match the pins for your board. All pins should map from index 0 to PinCount-1
13. Find and modify GetGpioPinIRQNumber to match what is being set in gpio_irq_init in mbed (`<mbed root>\libraries\mbed\targets\hal\TARGET_STM\TARGET_STM32L1\gpio_irq_api.c`)
14. This step is purely pattern matching: go through the board schematic, and determine which pins are used for which peripherals, and change values as appropriate
15. Find the file "PeripheralPins.c" for your board. Determine the appropriate peripherals and SerialPort IRQ numbers from there.
16. Rebuild the solution, and if everything was done correctly, it should all compile.

## Modifying the Makefile
1. Go to [mBed](http://developer.mbed.org/compiler), create a simple program for your board, right click on the project, click Export, and Export for GCC (ARM Embedded)
2. This will download a .ZIP file. Move the folder `<zip>\<program name>\mbed\TARGET_<platform>` to `<llilum repo>\Zelig\mbed\`
3. Open the Makefile in the .ZIP and open the Makefile in `<llilum repo>\Zelig\LLVM2IR_results\mbed\simple`
4. In the makefile from the repo, add a section for STM32L152
5. Copy all of the necessary variables from the ZIP makefile to the llilum repo one (SYS_OBJECTS, INCLUDE_PATHS, CC_FLAGS, etc.)
6. Replace all instances of "./mbed" for the filepath to $(MBED_ROOT)
7. Add "OBJECTS += $(TARGET)\startup_stm32l152xe.o" or the equivalent of that
8. Remove the startup_stm32l152xe.o file from the SYS_OBJECTS variable
9. Find the correct startup file in mbed (startup_stm32l152xe.S in a TOOLCHAIN_GCC_ARM directory) and copy it to the $(LLILUM_ROOT)\Zelig\os_layer\ARMv7M\Vectors directory. 
10. You will need to define the heap size (and optionally the stack size) here.  Use the file startup_LPC17xx.s as a guide.  Defining __HeapBase and __HeapEnd are critical because they are used to define the Llilum heap location. __HeapEnd must be placed immediately after __HeapBase in memory because the address of the symbols are used to dynamically determine the size defined for the Llilum heap (in $(LLILUM_ROOT)\Zelig\os_layer\ports\mbed\mbed_core.cpp).
11. Compare the new section to the existing ones - ensure that everything looks like it matches the correct patterns
12. (Optional) Copy main.cpp from the ZIP file to `<llilum repo>\Zelig\LLVM2IR_results\mbed\simple`, comment out the line in the makefile that adds the `.\Microsoft.Zelig.Test.mbed.Simple_opt.o` target and run `make TARGET=STM32L152` from a command prompt. If everything builds, and then you have most likely succeeded.
13. If you completed step 11, uncomment the `.\Microsoft.Zelig.Test.mbed.Simple_opt.o` line  

## Testing the compilation
**If you are not able to build and run your code at the end of this section, DO NOT MOVE ON. Something is broken, and needs to be fixed before spending time on the SDK**  
  
1. In the Llilum solution, MbedOSAbstraction project, there is a mbed_helpers.h file. Open it, and add a section for the STM32L152 header
2. Find the "simple" project, and add a reference to "STM32L152.dll", which should be in the same place as "LPC1768.dll"
3. Open the Program.cs file in the "simple" project, and comment out all of the #defines at the top, except for "USE_GPIO"
4. Add a #define for STM32L152, and add code throught Program.cs, that sets up the right pins, and uses correct ports (essentially, modify the program so that it just blinks an LED)
5. Right click, and build the simple project. If it builds, you can move on.
6. Follow the steps from [demo](https://github.com/NETMF/llilum/wiki/Demo) to set up the FrontEnd project. Notice the parameter being passed to debug
7. Copy mbed_simple_LPC1768.FrontEndConfig, paste it there, and call it mbed_simple_STM32L152.FrontEndConfig
8. Change the argument passed to FrontEnd debug, so that it reads `-cfg ..\..\..\..\Zelig\CompileTime\CodeGenerator\FrontEnd\mbed_simple_STM32L152.FrontEndConfig`
9. Open that file, and change all instances of "LPC1768" to "STM32L152" (there should be at least 3)
10. Set FrontEnd as the startup project, hit F5, and hope for the best
11. If the compiler succeeded, open a command prompt to `<llilum repo>\Zelig\LLVM2IR_results\mbed\simple` (the output should be in there)
12. Run `build.bat STM32L152` and if that succeeds, a folder called STM32L152 will be created, and the cmd window will not show any errors
13. Create a new deploySTM32L152.bat file, or copy STM32L152\mbed_simple.bin onto the connected mBed device
14. (Optional) If blinky appears to work, modify Program.cs to make it blink slower. Why? Because when mBed runs into an error, it also starts blinking
  
### Congratulations for getting this far!
  
If building with a makefile works for you, you can stop here. However, if you would like to have others use your board from the SDK, let's continue.  
  
## Creating a new .props file for the SDK  
**Do this part very carefully. This essentially becomes your makefile, and minor errors are hard to find**  
  
1. Copy `<llilum repo>\VisualStudio\LlilumApplicationType\Llilum\1.0\Platforms\Llilum_LPC1768.props` into Llilum_STM32L152.props
2. Notice how the first 6 elements (such as LlilumTargetBrandLPC1768) match the folder structure in `<llilum repo>\Zelig\mbed\`
3. Change all instances of LPC1768 to STM32L152
4. Open the makefile that you modified earlier, and the folder `<llilum repo>\Zelig\mbed\`
5. Navigate the folder structure, and replace the contents of the Board, Brand, Toolchain, Class, and BoardMbed elements
6. If verifying this is too difficult, these values can be taken out of the makefile (just replace "$(MBED_ROOT)" with "$(LlilumSDK)mbed\")
7. All fields that do not use those elements that were replaced can be taken right out of the makefile. The mapping is below:  
  
* LD_FLAGS -> LlilumLinkAdditionalOptions  
* CC_SYMBOLS -> LlilumClPreprocessorDefs (remove -D and replace with ;)  
* INCLUDE_PATHS -> LlilumClAdditionalIncludes  
* SYS_OBJECTS -> LlilumLinkAdditionalDeps  
* CPU -> LlilumClAdditionalOptions  
* LIBRARY_PATHS -> LlilumLinkAdditionalLibDirs  
* LD_SYS_LIBS -> LlilumLinkDeps  
  
**NOTE: Remember the startup_STM.o file we removed from the original makefile? PUT IT BACK IN! It came from the SYS_OBJECTS variable, so put it into LlilumLinkAdditionalDeps **
  
## Modifying the SDK
1. Open the solution in `<llilum repo>\VisualStudio\LlilumProjectType`
2. Open the Native.vcxproj file. Find the section for LPC1768, copy it, paste it below, and change all instances of LPC1768 to STM32L152 in it
3. Find the .props file imports. Add a reference to the props file you just created
4. Copy the LPC1768.FrontEndConfig file, paste it, and change all instances of LPC1768 to STM32L152
5. In MyTemplate.vstemplate, add a new ProjectItem for STM32L152
6. In Native.vcxproj, add a reference to the new FrontEndConfig file (towards the end, search for LPC1768.FrontEndConfig)
7. Open Managed.csproj, and add a reference to the STM32L152.dll, just like the one for LPC1768
8. Open `<llilum repo>\VisualStudio\LlilumApplicationType\Llilum\1.0\1033\Llilum.xml` and add a new enum for STM32L152
9. In Visual Studio, click tools, Extentions and Updates, and uninstall the Llilum Application VSIX
10. Delete the LLILUM_SDK environment variable
11. Re-create and install the SDK (best to put it in the same place that it used to be) using the [same steps as before](https://github.com/NETMF/llilum/wiki/SDK-User-Guide)
  
  
**Open a new Visual Studio instance, create a Llilum Application, go to Native project properties/Llilum tab, change the Board to STM32L152 (which should 
now show up), and clicking apply should fill in all missing fields. Make sure that the FrontEndConfig property is pointing to the correct one (it will not 
be by default, use the full path if possible). Modify Program.cs to use the correct pins and board reference. CTRL+SHIFT+B to build everything after changing the FrontEndConfig. If the build succeeds, that's a good sign**
  
  
**NOTE: Deploy may not work on some boards. The .bin file is located in `<solution dir>\ARM\Debug\` take that file, and put it on your board however you would like**