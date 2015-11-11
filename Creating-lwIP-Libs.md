In order to create a new Board Support Package, libs for lwIP need to be created. If a user ever needs to modify lwIP itself, the libs must be recreated as well. There are 4 of them:
* **lwIP:** Contains all of the lwIP core object files
* **mbedeth:** Contains the mBed EthernetInterface and supporting objects
* **lwIPeth:** Contains the ethernet driver objects for the board (may not be available on all boards)
* **lwipsysarch:** Contains the objects found in the lwIP-sys folder  
  
## Create and export an mBed lwIP project
1. Navigate to this page: https://developer.mbed.org/handbook/Ethernet-Interface
2. Import the TCPSocket_HelloWorld program for a board that supports lwIP
3. Right click on the program, click Export, and export for the ARM GCC toolchain

## Modify and compile the program
1. Extract the exported project out of the ZIP file
2. Open mbed-rtos\rtos\Semaphore.h and add the header `void init(void);` and a private variable `int32_t _count;`
3. Open Semaphore.cpp, comment out everything in the constructor, and replace it with `_count = count;`
4. Create a method `void Semaphore::init(void){_osSemaphoreId = osSemaphoreCreate(&_osSemaphoreDef, count);}`
5. Open main.cpp and comment out everything in main() except for the while loop at the end
6. Copy everything from `<llilum repo>\Zelig\mbed\EthernetInterface` to where the extracted EthernetInterface code resides, and replace all files
7. Open EthernetInterface.h and add `#include "rtos.h"` towards the top
8. Run MAKE and everything should compile

## Split up the object files
1. Open the Makefile and look at the OBJECTS variable at the top
2. This needs to be split up into the different libs. Everything under lwip goes into the lwIP lib. Everything under lwip-eth goes into the lwIPeth lib. Semaphore.o and EthernetInterface.o go into the mbedeth lib.
3. For each lib run the following: `<ARM_GCC>\bin\arm-none-eabi-ar.exe --target=elf32-little  -v -q <lib name> <list of object files>`
4. For LPC1768 it looks like the following:   
  
liblwIP.a: `./EthernetInterface/lwip/netif/etharp.o ./EthernetInterface/lwip/netif/ethernetif.o ./EthernetInterface/lwip/netif/slipif.o ./EthernetInterface/lwip/netif/ppp/auth.o ./EthernetInterface/lwip/netif/ppp/chap.o ./EthernetInterface/lwip/netif/ppp/chpms.o ./EthernetInterface/lwip/netif/ppp/fsm.o ./EthernetInterface/lwip/netif/ppp/ipcp.o ./EthernetInterface/lwip/netif/ppp/lcp.o ./EthernetInterface/lwip/netif/ppp/magic.o ./EthernetInterface/lwip/netif/ppp/md5.o ./EthernetInterface/lwip/netif/ppp/pap.o ./EthernetInterface/lwip/netif/ppp/ppp.o ./EthernetInterface/lwip/netif/ppp/ppp_oe.o ./EthernetInterface/lwip/netif/ppp/randm.o ./EthernetInterface/lwip/netif/ppp/vj.o ./EthernetInterface/lwip/api/api_lib.o ./EthernetInterface/lwip/api/api_msg.o ./EthernetInterface/lwip/api/err.o ./EthernetInterface/lwip/api/netbuf.o ./EthernetInterface/lwip/api/netdb.o ./EthernetInterface/lwip/api/netifapi.o ./EthernetInterface/lwip/api/sockets.o ./EthernetInterface/lwip/api/tcpip.o ./EthernetInterface/lwip/core/def.o ./EthernetInterface/lwip/core/dhcp.o ./EthernetInterface/lwip/core/dns.o ./EthernetInterface/lwip/core/init.o ./EthernetInterface/lwip/core/mem.o ./EthernetInterface/lwip/core/memp.o ./EthernetInterface/lwip/core/netif.o ./EthernetInterface/lwip/core/pbuf.o ./EthernetInterface/lwip/core/raw.o ./EthernetInterface/lwip/core/stats.o ./EthernetInterface/lwip/core/tcp.o ./EthernetInterface/lwip/core/tcp_in.o ./EthernetInterface/lwip/core/tcp_out.o ./EthernetInterface/lwip/core/timers.o ./EthernetInterface/lwip/core/udp.o ./EthernetInterface/lwip/core/ipv4/autoip.o ./EthernetInterface/lwip/core/ipv4/icmp.o ./EthernetInterface/lwip/core/ipv4/igmp.o ./EthernetInterface/lwip/core/ipv4/inet.o ./EthernetInterface/lwip/core/ipv4/inet_chksum.o ./EthernetInterface/lwip/core/ipv4/ip.o ./EthernetInterface/lwip/core/ipv4/ip_addr.o ./EthernetInterface/lwip/core/ipv4/ip_frag.o ./EthernetInterface/lwip/core/snmp/asn1_dec.o ./EthernetInterface/lwip/core/snmp/asn1_enc.o ./EthernetInterface/lwip/core/snmp/mib2.o ./EthernetInterface/lwip/core/snmp/mib_structs.o ./EthernetInterface/lwip/core/snmp/msg_in.o ./EthernetInterface/lwip/core/snmp/msg_out.o`  
  
liblwIPeth.a: `./EthernetInterface/lwip-eth/arch/TARGET_NXP/lpc17_emac.o ./EthernetInterface/lwip-eth/arch/TARGET_NXP/lpc_phy_dp83848.o`  
  
libmbedeth.a: `./mbed-rtos/rtos/Semaphore.o ./EthernetInterface/EthernetInterface.o`  
  
## Create the sys-arch lib
1. Place liblwIPeth.a and libmbedeth.a into `<llilum repo>\Zelig\mbed\<TARGET BOARD>\TOOLCHAIN_GCC_ARM`
2. If liblwIP.a needs to be replaced, put it in `<llilum repo>\Zelig\lwip\lwip\lib`
3. Open the makefile in `<llilum repo>\Zelig\LLVM2IR_results\mbed\simple`
4. Create the appropriate settings section for your board and ensure that it has `LIBRARIES += -llwIP -lmbedeth` and `LIBRARY_PATHS += -L$(MBED_LIBS)/lwip/lwip/lib`
5. At the top, set `COMPILE_SYS_ARCH = 1`
6. Compile the simple llilum test program for your board
7. Run build.bat for your board
8. Change directory to the build.bat output directory
9. Run `<ARM_GCC>\bin\arm-none-eabi-ar.exe --target=elf32-little  -v -q liblwipsysarch.a checksum.o memcpy.o sys-arch.o`
10. Copy the lib into `<llilum repo>\Zelig\mbed\<TARGET BOARD>\TOOLCHAIN_GCC_ARM` 