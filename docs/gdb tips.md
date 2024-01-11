---
share: true
---

# CppCon 2015: Greg Law " Give me 15 minutes & I'll change your view of GDB"
https://www.youtube.com/watch?v=PorfLSr3DDI

## TUI
`ctrl x + a` open TUI
`ctrl l` repaint screen. sometime tui gets weird as tends to happen

## Multiple windows
`ctrl x 2` opens multiple windows, repeat to cycle thru
`ctrl x 1` goes back to 1 window
Use `ctrl p` & `ctrl n` to cycle thru previous commands. (arrows scroll code in TUI mode)

## Python interpreter
`python print('hello world')`
Run just `python` to drop into interpreter
Can define functions that you can call from command line, and functions that can be first class gdb commands

Can manage breakpoints from Python

## Reversible debugging
```
break main # breakpoint 2
break _exit.c:32 # breakpoint 3
command 3
	run # restarts program (program crashes inconsistently)
command 2
	record # this will start recording on startup
	continue
# program eventually crashes
reverse-step-i
# Can setup watchpoints when going backwards
watch *(long**) 0x7ffffffffdc98
reverse-continue
```

Can verify if you have access to a memory location by running
```
x 0x5e4c5d00
```
will print "Cannot access memory at address" if it's not accessible

## Other commands
`tbreak` temporary breakpoint
`rbreak` regex breakpoint
`break xxx if yyy` conditionally break at xxx if condition yyy holds
`commands` list of commands to be executed when a breakpoint is hit
`silet` command to suppress output on breakpoint hit
`save breakpoints` save list of breakpoints to a script
`save history` save history of executed gdb commands
`call` call a function in the interior
`watch -l` watchpoint based on address (location)
`rwatch` read watchpoint
`info line foo.c:42` show PC for line
`info line * $pc` show line begin/end for current program counter
`thread apply all bt` show backtrace for every thread
`dprintf` dynamic printf
python: define custom commands by inheriting from `gdb.Command` class
python: hook events to invoke python functions using `gdb.events.stop.connect`

# Run until segfault
Run the following gdb commands:
```
set pagination off
break exit
commands
run
end
run
# The final run starts the process
```
https://stackoverflow.com/a/6546674