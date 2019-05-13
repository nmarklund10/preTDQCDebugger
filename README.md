# csDbg Specification

The CSD Remote Debugger (csDbg) is a learning project intended to prepare an
uncertified developer for the Basic Skill Level Exam. The project should be
guided by a mentor and developed by the mentee developer.

## Goal

The goal of the project is to implement a remote debug service that 
interacts with a user client over a custom protocol. The debug service should
be able to attach to or start a process on the same machine that it is 
currently running on and communicate process state and information with client
(contents of registers, memory address, etc.).

## Overview

The service (`csdbgd`) will be written in C and reside on the system with the
executable to be debugged (referred to as the `target` executable). Refer to
the [service specification](csdbgd.md) document for the full service 
specification.

The client will initially be implemented as a command line Python application
(`csdbgclient.py`) but will optionally be extended into a web service 
implemented in a common web framework like Django or Flask that supports a full
GUI display of the debugging information of the target. Refer to the 
[client specification document](csdbg-client.md) specification for the full
client specification.

The custom protocol (`csdbgp`) will be implemented over TCP on port 1337. Refer
to the [protocol specification](protocol.md) document for the full 
specification of the protocol.

## Learning Tasks

The csDbg project will be implemented in incremental stages called "learning
tasks". You can all of the learning tasks in the 
[Learning Tasks](learningtasks/) directory of this repository. It is 
recommended that the mentor assigns the learning tasks in order as the mentee
progresses through the project. 

There is nothing wrong with doing learning tasks out of order - the learning 
tasks progress in order of ease, from simplest to most complex. In an actual 
project, it's often advisable to do the most complex tasks upfront, and finish
with the simple cosmetic tasks. The learning tasks of this project progress in
the order that they do for the purpose of teaching, not for the purpose of
shipping a working product.

Start at [Learning Task 1](learningtasks/task1.md)
