# Learning Task 1

## Learning Objectives

* Fork a GitLab repository
* Implement Python command line argument parsing
* Implement Python TCP network sockets
* Implement Python debug print options

## Issues

* Locate the `csdbg-client` repository on GitLab and create a branch named
  `<your name>-master` (e.g. `kellas-master`).
* Create the file `csdbg.py` and implement the following functionality
  using Python 3.5 or newer:
    * Parse the following arguments from the command line: `--ip <IP address>`,
      `--target <target executable>`, `--pid <pid number>`, and 
      `-v | --verbose`
    * Ensure that the IP address is always provided, but that only one of the
      two options of target executable and pid are provided.
    * Print out the provided IP address and either the file name provided or
      the process ID provided
    * Send a TCP packet to the input IP address on port 1337 according to the
      [protocol specification](#Protocol) below
    * If the `-v` or `--verbose` flag is provided, print the hex encoded
      contents of the protocol data before it is sent.

## Protocol

The csdbg protocol will evolve as more tasks are implemented, but for Learning
Task 1, the protocol will be very simple:

magic bytes (4 bytes): 0x6373db67

message type (1 byte): initiate session - 0x00

session start type (1 byte): if pid was provided - 0x00; if target executable 
was provided - 0x01  

data (variable): if pid was provided - 4 byte pid; if target 
executable was provided - 1 byte string length of the target executable name, 
followed by ASCII encoded target executable name  

Example:

If the user provides the following input:
```
$ python3 csdbg.py --ip 127.0.0.1 --target /bin/ls
```

Then the program should output a TCP packet sent to 127.0.0.1:1337 of the 
following form:

| Magic Bytes | Message Type | Start Session Type | Data - String Length | Data - String |
|-------------|--------------|--------------------|----------------------|---------------|
| 0x63 0x73 0xdb 0x67 | 0x00 | 0x01 | 0x7 | 0x2f 0x62 0x69 0x6e 0x2f 0x6c 0x73 |


If the user provides the following input:
```
$ python3 csdbg.py --ip 127.0.0.1 --pid 1234
```

Then the program should output a TCP packet sent to 127.0.0.1:1337 of the
following form:

| Magic Bytes | Message Type | Start Session Type | Data - Pid |
|-------------|--------------|--------------------|------------|
| 0x63 0x73 0xdb 0x67 | 0x00 | 0x00 | 0x00 0x00 0x04 0xd2 |
