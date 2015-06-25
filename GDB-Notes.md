## Articles about debugging languages not supported by GDB with GDB

* http://www.mono-project.com/docs/debug+profile/debug/
* https://golang.org/doc/gdb

## Dwarf Specs

* http://dwarfstd.org/doc/dwarf-2.0.0.pdf
* http://dwarfstd.org/doc/Dwarf3.pdf

## CMSIS-DAP GDB Server

The only FOSS (Apache Lic.) implementation I found comes from the pyOCD project (https://github.com/mbedmicro/pyOCD).
This is a library to debug and write devices using CMSIS-DAP from Python.
That being said, the gdb server can be built into a standalone binary without dependencies on Python so we might be able to use it. 

## Visual Studio GDB

Visual Studio 2015 comes with support for GDB via the Debug.MIDebugLaunch command.

There also exist two plugins to allow using Visual Studio as a GDB frontend, both of them commercial, so they won't work for us.

* http://www.visualgdb.com/
* http://www.wingdb.com/

