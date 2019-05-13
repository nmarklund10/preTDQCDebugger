# csDbg Service Specification

The CSD Remote Debugger implements a service in C. This service executable is
named `csdbgd` and listens for a connection for the CSD Remote Debugger client
component.

All communication of state of the target program and receiving commands from
the user will be over the [csDbg protocol](protocol.md)

## Features

Features of the csDbg service are:
* Start an program and begin debugging at its entry point
* Query list of pids and program names for all running processes, allowing the
  client to select one to attach to
* Attach to a running program identified by its pid (requires service run with
  root privileges)
* Set a breakpoint in the program at a specified address
* Enable or disable breakpoints
* View all breakpoints
* Delete breakpoints
* View register contents when target program is stopped
* Set register contents when target program is stopped
* View memory contents when target program is stopped
* Set memory contents when target program is stopped
* Communicate signals received that cause the target program to stop
* Continue execution of the target program
* Kill target program

## Constraints

Initial support of the csDbg is only for x86 64 bit programs running on Linux.
* Only general purpose registers supported in initial release (no floating
  point registers)
* Position Independent Executables (PIE) are not supported - code addresses
  of the target text segment should be static (this will be one of the first
  fixes in future releases)
* Multi-threaded programs are not supported

## Future Work

Prioritized:
* Support PIE binaries by calculating offsets from the target text segment
* Support x86 32-bit binaries
* Parse symbol table for function names to break at start of function
* Support multi-thread programs
* Support ARMv7 binaries (when service is compiled as ARM binary - not
  emulated)
