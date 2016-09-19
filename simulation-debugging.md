# 模拟过程调试

由于模拟过程跑在上位机上，所以你可以使用一切桌面环境的开发工具。

## CLANG Address Sanitizer (Mac OS, Linux)

The Clang address sanitizer can help to find alignment (bus) errors and other memory faults like segmentation fauls. The command below sets the right compile options.

```sh
make clean # only required on first address sanitizer run after a normal build
MEMORY_DEBUG=1 make posix jmavsim
```

## Valgrind

```sh
brew install valgrind
```

or

```sh
sudo apt-get install valgrind
```

<aside class="todo">
Add instructions how to run Valgrind
</aside>

## Start combinations

SITL can be launched with and without debugger attached and with either jMAVSim or Gazebo as simulation backend. This results in the start options below:

```sh
make posix_sitl_default jmavsim
make posix_sitl_default jmavsim___gdb
make posix_sitl_default jmavsim___lldb

make posix_sitl_default gazebo
make posix_sitl_default gazebo___gdb
make posix_sitl_default gazebo___lldb

make posix_sitl_lpe jmavsim
make posix_sitl_lpe jmavsim___gdb
make posix_sitl_lpe jmavsim___lldb

make posix_sitl_lpe gazebo
make posix_sitl_lpe gazebo___gdb
make posix_sitl_lpe gazebo___lldb
```

This will start the debugger and launch the SITL application. In order to break into the debugger shell and halt the execution, hit ```CTRL-C```:

```bash
Process 16529 stopped
* thread #1: tid = 0x114e6d, 0x00007fff90f4430a libsystem_kernel.dylib`__read_nocancel + 10, name = 'px4', queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
    frame #0: 0x00007fff90f4430a libsystem_kernel.dylib`__read_nocancel + 10
libsystem_kernel.dylib`__read_nocancel:
->  0x7fff90f4430a <+10>: jae    0x7fff90f44314            ; <+20>
    0x7fff90f4430c <+12>: movq   %rax, %rdi
    0x7fff90f4430f <+15>: jmp    0x7fff90f3fc53            ; cerror_nocancel
    0x7fff90f44314 <+20>: retq
(lldb)
```

In order to not have the DriverFrameworks scheduling interfere with the debugging session ```SIGCONT``` should be masked in LLDB and GDB:

```bash
(lldb) process handle SIGCONT -n false -p false -s false
```

Or in the case of GDB:

```
(gdb) handle SIGCONT noprint nostop
```

After that the The lldb or gdb shells behave like normal sessions, please refer to the LLDB / GDB documentation.
