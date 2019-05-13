# Learning Task 3

## Learning Objectives

* Implement C service
* Implement C TCP network sockets
* Building a C program with a Makefile

## Issues

* Locate the `csdbg-service` repository on GitLab and create a branch named
    `<your name>-master` (e.g. `kellas-master`) and implement the following
    functionality in C as a file named `csdbgd.c` (note the trailing 'd'):
    * Create a socket that listens for and accepts TCP connections on port
      1337.
    * Receive the `csdbg-protocol` `session initiate request` message 
      (implemented in the python client in [Learning Task 1](task1.md).
    * Print to the command line either: the pid or the target binary name
      requested in the `session initiate request` message.
    * Respond to the client with a `session initiate acknowledge` message
      (described below).
    * Create debug print statement that prints the bytes of the message prior 
      to transmission.
* Create a Makefile in the same directory that builds you C program with `gcc`
  when the command `make` is executed. 
    * Implement a `make clean` command that removes any build files as well.
* Remember to follow the CSD C style guide (found on the `cybbh` GitLab) as
  you go.

## Protocol

An acknowledge message should be sent from the service to the client if
a valid `session initiate request` message is received and the debugger is
able to debug the requested target. For now, the task is to just implement
the receiving of a valid request message, parsing it, and immediately sending
an acknowledgement message (later tasks will involve starting or attaching to
the requested target program).

A `session initiate acknowledge` message is described in 
[Protocol](../protocol.md). In summary:
* `magic_bytes` field stays the same - `0x63 0x73 0xdb 0x67`
* `message_type` field will be set to `0x01` to indicate acknowledgement
* `session_id` field will be a unique value for each connection. You will need
  to implement this in a way that is managed per connection.
* `target_architecture` field should be set to `0x01` representing that your
  service is deployed on a host system that is a 64 bit x86 architecture

Your service should respond to your client with the following:

| Magic Bytes | Message Type | Session ID | Target Architecture |
|-------------|--------------|------------|---------------------|
| 0x63 0x73 0xdb 0x67 | 0x01 | Variable - 4 bytes | 0x01 |
