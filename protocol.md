# csDbg Protocol

csDbg protocol is a TCP protocol over port 1337. The protocol is used to
communicate between the csDbg service and a client.

REDO ENTIRE THING

## General Structure

The csDbg protocol exists in the data field of a TCP packet. All fields are
in network byte order.

The first field in a csDbg packet is the `magic_bytes` field. This field is 4
bytes wide and will always be set to the value `0x63 0x73 0xdb 0x67`.

The second field in the csDbg packet is the `message_type` field. This field is
1 byte wide and can have one of the following values:

* `0x00`: Initiate request message
* `0x01`: Initiate response message
* `0xdd`: Command request message
* `0xaa`: Command respoonse message
* `0xff`: Error message

All fields following the `message_type` field are dependent on the
`message_type` field.

### Session Initiate Request

Communicates request from the client to the server for new debug session by
providing either the process ID of the process to attach to or the path to the
executable on the target system to begin debugging.

* field 1: `session_type` (1 byte)
* field 2: depends on `session_type`
* field 3: depends on `session_type`

Valid values for session type:

* `0x00`: `ATTACH` to target process
* `0x01`: `START` new target process

If `session_type` is `ATTACH`, `field 2` is set to a 4 byte `pid` of the
process to attach to.

If `session_type` is `START`, `field 2` is set to a 1 byte `target_name_len`
and `field 3` is set to a variable length character array of the the path to
the target process, `target_name`.

### Session Initiate Acknowledge

Communicates that server acknowledges the client's request to begin a debug
session and provides the client a unique session ID and specifies the target
architecture.

* field 1: `session_id` (4 bytes)
* field 2: `target_architecture` (1 byte)

The `session_id` field should be unique to the current debug session (created
when the initiate session message is received).

The `target_architecture` field represents the architecture of the host system
of the `csdbg` service, and should match the architecture of the binary file
being debugged.

Possible values for `target_architecture`:
* `0x00`: x86 - 32 bit
* `0x01`: x86 - 64 bit
* `0x02`: ARMv7

### Target Status message

**TODO: GET RID OF STATUS MESSAGE**

Roll it up into command response message for *continue* and *single step*
messages

Communicates the status of the target process (running, stopped, exit) and any
amplifying information (signal received, exit code).

* field 1: `session_id` (4 bytes)
* field 2: `target_architecture` (1 byte)
* field 3: `session_status` (1 byte)
* field 4: `info` (1 byte)

Possible values for `session_status`:

* `0x00`: Running
* `0x10`: Stopped
* `0xff`: Exit

If `session_status` is `Stopped`, `info` field corresponds to signal value
received that caused the process to stop. These values are recorded in
your system's `signal.h`.

If `session_status` is `Exit`, `info` field is the exit status received from
the process.

### Command Request Message

Sends a command from the client to the debug service to execute (begin
program execution, set breakpoint, continue execution, view register, view
memory, set register, set memory, end session).

* field 1: `session_id` (4 bytes)
* field 2: `target_architecture` (1 byte)
* field 3: `command` (1 byte)
* field 4: `arguments` (optional, variable size)

Possible values for `command_type`:

* `0x00`: Continue
* `0x10`: Single-Step
* `0x20`: Read registers
* `0x30`: Write register (specified in arguments)
* `0x40`: Read memory
* `0x50`: Write memory
* `0x60`: Set breakpoint
* `0x70`: Delete breakpoint
* `0x80`: List breakpoints (unimplemented)
* `0xff`: Kill

For values of optional `arguments` see [Commands](#commands) section.

### Command Response Message

Acknowledge good execution of command message request and transmit any data
as a result of the request.

* field 1: `session_id` (4 bytes)
* field 2: `target_architecture` (1 byte)
* field 3: `command_type` (1 byte)
* field 4: `data` (optional, variable size)

Possible values for `command_type` are same as in command message request.

For values of optional `arguments` see [Commands](#commands) section.

### Error message

Communicate error condition in previous request.

* field 1: `error_type` (1 byte)
* field 2: `error_len` (1 byte)
* field 3: `error_msg` (variable)

### Commands

Arguments listed for commands are transmitted in a `command request` message
and Data listed are transmitted in a `command response` message.

#### Continue Command

Continue target program execution.

Arguments:
* None

Data:
* `Session Status` (1 byte)
* `Info` (4 bytes)

Possible values for `session_status`:

* `0x00`: Running
* `0x10`: Stopped
* `0xff`: Exit

If `session_status` is `Running`, `info` is ignored.

If `session_status` is `Stopped`, `info` field corresponds to signal value
received that caused the process to stop. These values are recorded in
your system's `signal.h`.

If `session_status` is `Exit`, `info` field is the exit status received from
the process.


#### Single-Step Command

Execute one instruction of the target program and send a SIGSTOP signal.

Arguments:
* None

Data:
* Same as for [Continue Command](#continue-command)

#### Read Registers Command

Read all general purpose registers of the target program when stopped.

Arguments:
* None

Data (*Architecture dependent - x86 64 bit listed below*):
* field 1: `rax` (8 bytes)
* field 2: `rbx` (8 bytes)
* field 3: `rcx` (8 bytes)
* field 4: `rdx` (8 bytes)
* field 5: `rdi` (8 bytes)
* field 6: `rsi` (8 bytes)
* field 7: `r8` (8 bytes)
* field 8: `r9` (8 bytes)
* field 9: `r10` (8 bytes)
* field 10: `r11` (8 bytes)
* field 11: `r12` (8 bytes)
* field 12: `r13` (8 bytes)
* field 13: `r14` (8 bytes)
* field 14: `r15` (8 bytes)
* field 15: `rbp` (8 bytes)
* field 16: `rsp` (8 bytes)
* field 17: `rip` (8 bytes)
* field 18: `eflags` (8 bytes)

#### Write Register Command

Write one register value of the target program when stopped.

Arguments:
* field 1: `register` (1 byte)
* field 2: `value` (8 bytes *x86 64 bit*)

`register` values (*Architecture dependent - x86 64 bit listed below*):
* 0x00 - `rax`
* 0x01 - `rbx`
* 0x02 - `rcx`
* 0x03 - `rdx`
* 0x04 - `rdi`
* 0x05 - `rsi`
* 0x06 - `r8`
* 0x07 - `r9`
* 0x08 - `r10`
* 0x09 - `r11`
* 0x0a - `r12`
* 0x0b - `r13`
* 0x0c - `r14`
* 0x0d - `r15`
* 0x0e - `rbp`
* 0x0f - `rsp`
* 0x10 - `rip`
* 0x11 - `eflags`

Data:
* None

#### Read Memory Command

Read a word from memory address.

Arguments:
* field 1: `address` (8 bytes *x86 64 bit*)

Data:
* field 1: `value` (8 bytes *x86 64 bit*)

#### Write Memory Command

Write a word to memory address.

Arguments:
* field 1: `address` (8 bytes *x86 64 bit*)
* field 2: `value` (8 bytes *x86 64 bit*)

Data:
* None

#### Set Breakpoint Command

Set breakpoint at memory address.

Arguments:
* field 1: `address` (8 bytes *x86 64 bit*)

Data:
* field 1: `breakpoint ID`

#### Delete Breakpoint Command

Delete a breakpoint by ID number.

Arguments:
* field 1: `breakpoint_ID` (4 bytes)

Data:
* None

#### List Breakpoints Command

**Not currently supported.**

List all breakpoints.

#### Kill

Send kill signal to target process.

Arguments:
* None

Data:
* None
