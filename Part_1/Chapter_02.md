# Chapter 2 - Getting Started

## Conventions Used in this Book

items in **bold** are typed in the keyboard

utilities are typed in sans serif

filenames also in bold

small caps for keys / characters

most examples show the prompt, but omit the `RETURN` keystroke

optional passages are in gray boxes

implicit `http://` prefix on urls

## Logging In from a Terminal or Terminal Emulator

`/etc/issue` - identifies version of linux in human reable form, not like `uname -a`

"last login from date on `tty`"

`tty` is a (program?) thing you can invoke to tell you which terminal you are on?

...subshells have the same `tty` value on my Mac

```bash
$ tty
/dev/ttys000
$ tty && echo $(tty)
/dev/ttys000
/dev/ttys000
```

Interestingly, my Ubuntu `tty` value is different:

```bash
$ ssh sonlite
tty[sonlite@web612 ~]$ tty
/dev/pts/5
```

`telnet` sends passwords in plaintext

`/etc/motd` - message of the day

`$TERM` - establishes the pseudographical characteristics of a character-based terminal

```bash
[sonlite@web612 ~]$ echo $TERM
xterm-256color
```

## Working with the Shell

pseudographical interfaces bridge the gap between text terminals and GUIs

(even simple editors are pseudographical elements, because they allow representing and manipulating an array of bytes)

(and even terminals themselves are pseudographical when you think about it... as characters are encoded out of binary...)

### Which Shell are You Running?

`ps` - process status, with no args shows you open terminals:

```bash
$ ps
  PID TTY           TIME CMD
18137 ttys000    0:00.64 -bash
20846 ttys001    0:00.23 -bash
21151 ttys002    0:00.21 -bash
```

weird thing: `ps` is always the PID of the currently running terminal, however... once I start to redirect it, things change:

```bash
$ echo "$(ps)" | tail -n 1 | cut -d ' ' -f 1
22655
$ echo "$(ps)" | tail -n 1 | cut -d ' ' -f 1
22679
```

But this isn't because the PID of my terminal is running... Is `ps` grabbing the PID of whatever is being run after it? (That is, is it operating like `time`?) No. But rather because `ps` shows the "process status" of the process and any children processes.

And this lead to another interesting understanding. When you pipe (`|`), you do not run each command in succession, but rather they are all running at the same time, because... if it were not that case, how could `ps` know the pid of the commands after it?

Two examples prove this:

```bash
$ ps | cat
  PID TTY           TIME CMD
18137 ttys000    0:01.00 -bash
23187 ttys000    0:00.00 cat
$ echo "$(ps)" | cat
  PID TTY           TIME CMD
18137 ttys000    0:01.01 -bash
23207 ttys000    0:00.00 -bash
23208 ttys000    0:00.00 cat
```

Both when piping and using a subshell, it still knows about the PID that `cat` has.


delete a character: `Ctrl` + `H`
delete a word: `Ctrl` + `W`
suspend a program: `Ctrl` + `Z`
bring it back to the front: `fg`


That puts it in the background, but that means it's still running, so probably `ps` knows about it... let's see...

```bash
$ sleep 1000
^Z
[1]+  Stopped                 sleep 1000
$ sleep 1000
^Z
[2]+  Stopped                 sleep 1000
$ ps
  PID TTY           TIME CMD
18137 ttys000    0:01.25 -bash
24085 ttys000    0:00.00 sleep 1000
24094 ttys000    0:00.00 sleep 1000
```

But, it's definitely not running, it truly is suspended:

```bash
$ node
> var i = 0;
undefined
> var t = setInterval(() => console.log(++i), 5e3)
undefined
>
[3]+  Stopped                 node
$ sleep 10 && fg
node
1
> 2
3
```


delete a line: `Ctrl` + `U`

abort execution: `Ctrl` + `C`

if the program doesn't voluntarily respond to `SIGINT`, you can always `kill` it, and you can use `jobs` to find it's process ID

```bash
$ sleep 100 && sleep 50
^Z
[1]+  Stopped                 sleep 100
$ sleep 25
^Z
[2]+  Stopped                 sleep 25
$ jobs
[1]-  Stopped                 sleep 100
[2]+  Stopped                 sleep 25
```

We learn something interesting from the above as well, for the first job is just `sleep 100`, not `sleep 100 && sleep 50`. So `&&` must just queue things up to be run.

You can also use `kill` w/ a `-TERM` option, and specify the terminal id of the job to kill by `%x`, where x is the number of the id, e.g.:

```bash
$ jobs
[1]   Stopped                 sleep 100
[2]-  Stopped                 sleep 25
[3]+  Stopped                 sleep 10
$ kill -TERM %1
[1]   Terminated: 15          sleep 100
$ jobs
[2]-  Stopped                 sleep 25
[3]+  Stopped                 sleep 10
```

`kill -TERM %1`

Notice, the id 1 is gone, and the others still have their ids. Does the next job to be suspended get an incremented id, or does it replace 1?

```bash
$ sleep 1000
^Z
[4]+  Stopped                 sleep 1000
$ jobs
[2]   Stopped                 sleep 25
[3]-  Stopped                 sleep 10
[4]+  Stopped                 sleep 1000
```

Interestingly, there's a - and + sign in the mix now... hm...

`kill -TERM %%` kills the top of the stack...

And I've learned something interesting about `fg`:

`fg` allows me to pass a pid. When not passing an argument, it brings back the most recently suspended program, not the first, nor the last:

```bash
$ jobs
[1]-  Stopped                 sleep 1000
[2]+  Stopped                 sleep 100
$ fg
sleep 100
^Z
[2]+  Stopped                 sleep 100
$ fg 1
sleep 1000
^Z
[1]+  Stopped                 sleep 1000
$ fg
sleep 1000
^Z
[1]+  Stopped                 sleep 1000
$ fg 2
sleep 100
```

> `SIGINT` doesn't have to be honored by a program

## `su/sudo`: Curbing Your Power (root privileges)

don't experiment while working w/ root privileges

`$ su` doesn't work on my Mac... I wonder why...

`$ sudo bash` should be used instead.

This seems like a dangerous thing to do, so I should upgrade my $PS1 to warn me; a small diversion to put what I'm learning into practice: [weisjohn/sobell#13](https://github.com/weisjohn/sobell/issues/13)

## Where to Find Documenation

`man` - the system manual, UNIX format

`info` - GNU, has some hyperlinks

## The `--help` Option

pass as an arg to a command

## `man`: Displays the System Manual

`man` - show the system manual

Manual sections:

1. User commands
2. System calls
3. Subroutines
4. Devices
5. File Formats
6. Games
7. Miscellaneous
8. System Administration
9. Kernel
10. New

You can use these as arguments to `man`. Unless you specify, `man` uses the earliest occurrence in the manual.

E.g. `man passwd` - shows info about the command, vs. `man 5 passwd` shows info about `/etc/passwd`.

`apropos` - search the whatis database:

```
$ apropos rcp
rcp(1)                   - remote file copy
rsync(1)                 - faster, flexible replacement for rcp
$ apropos rsync
colorsyncd(8)            - ColorSync Daemon
rsync(1)                 - faster, flexible replacement for rcp
rsyncd.conf(5)           - configuration file for rsync in daemon mode
```


## `info`: Displays Information About utilities

hypertext system

`info` - more complete and up-to-date

keyboard shortcuts to navigate

`pinfo` might be easier, not on my mac

You can also just `cat` it to the terminal. e.g. `man man | cat`

## HOWTOs: Finding Out How Things Work

`/usr/share/doc` is pretty sparse

`gzcat` opens a zip file and cats it to the screen, without having to unzip first

## What to Do If You Cannot Log In

usernames / passwords are case sensitive

is it the wrong box

username invalid

## Changing Your Password

don't let other people have your passwords
