# TCP/IP Stack 

LLILUM supports TCP/IP through lwIP using the CMSIS-RTOS abstraction layer implementation of the mBed repo. 
Please see how to [re-create the lwIP libraries](https://github.com/NETMF/llilum/wiki/Creating-lwIP-Libs) in our wiki. 
lwIP is functional for all boards that have an Ethernet adapter, and it is currently tested on [K64F](https://developer.mbed.org/platforms/FRDM-K64F/). 

# CMSIS-RTOS

CMSIS-RTOS is a standard abstraction layer for application level threading and timer APIs. 

![CMSIS-RTOS diagram](https://github.com/NETMF/llilum/wiki/pics/CMSIS-RTOS.png) 

LLILUM exports all standard CMSIS-RTOS API functions and data structures so that any application code written for the CMSIS-RTOS API will just naturally work. Please see the LLILUM code for [CMSIS-RTOS implementation](https://github.com/NETMF/llilum/tree/dev/Zelig/Zelig/RunTime/Zelig/LlilumCMSIS-RTOS) in our code base. 

lwIP is implemented in mBed on top of CMSIS-RTOS, and therefore it just naturally work with the same identical architecture porting layer that it is used in mBed code base.


