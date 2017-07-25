# Chapter 1

OS == kernel + system programs

free as in speech, not as in beer

## History of UNIX and GNU-Linux

"four year effect" - college students learned it and took it with them

(that happened to me through Kevin / MBP Software w/ Mac OS X against the school-provided Windows)

GNU / Stallman: "everyone who has a copy has the freedom to cooperate with others in using it"

Linus makes Linux.

### The C Programming Language

Ken Thompson wrote UNIX OS in PDP-7 assembly in 1969.

Dennis Ritchie developed C, and w/ Thompson rewrote UNIX in C in 1973.

## Overview of Linux

kernel programming interface

multi user / scheduler

secure hierarchical file system

### The Shell: Command Interpreter and Programming Language

shell = command interpreter

4 most popular: bash, dash, tsh, zsh

filename generation (glob, etc.) & completion

device-independent IO

redirection is device-independent

the shell functions as a programming language

job control  - backgrounding jobs,

utilities

### Interprocess Communication

IPC - pipes & filters -

filter is a special kind of pipe that processes a stream of input to yield a stream of output

## Additional Features of Linux

GUIS

networking utilities

software dev - `bison`, `flex` - makes scanners, `make`
