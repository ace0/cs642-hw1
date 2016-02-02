# CS 642: Computer Security - Homework One

This homework assignment tasks you with understanding vulnerabilities 
in five target programs (four are required; one is extra credit). You must with work a partner.

It is due **February 22, 2014** by midnight local time. 

## The Environment 

You will test your exploit programs within a virtual machine
(VM) we provide, which is configured with Debian stable ("Lenny"), with ASLR (address
space layout randomization) turned off. 

See: https://github.com/ace0/vulnerability-demo for information about VMs and the VM image.

## The Targets

The `targets/` directory contains the source code for the targets, along with a Makefile for building them

Your exploits should assume that the compiled target programs are
installed setuid-root in `/tmp` -- `/tmp/target1`, `/tmp/target2`, etc.

To build the targets, change to the `targets/` directory and type
`make` on the command line; the Makefile will take care of building
the targets.

To install the target binaries in /tmp, run:
```
make install
```

To make the target binaries setuid-root, run:
```
make install
su
make setuid
```
Once you've run `make setuid` use `exit` to return to your user
shell.  Alternatively, you can keep a separate terminal or virtual
console open with a root login, and run `make setuid` (in the
`~user/hw1/targets` directory!) in that terminal or console.

Keep in mind that it'll be easier to debug the exploits if the targets
aren't setuid.  (See below for more on debugging.)  If an exploit
succeeds in getting a user shell on a non-setuid target in `/tmp`, it
should succeed in getting a root shell on that target when it is
setuid.  (But be sure to test that way, too, before submitting your
solutions!)

## The Exploits

The `sploits/`contains skeleton
source for the exploits which you are to write, along with a Makefile
for building them.  Also included is `shellcode.h`, which gives Aleph
One's shellcode.

## The Assignment

You are to write exploits, one per target. Each exploit, when run in
the virtual machine with its target installed setuid-root in `/tmp`,
should yield a root shell (`/bin/sh`).

Your task is to attack these targets. Targets 1-4 are required; target5 is extra credit.

## Hints 

Read the Phrack articles suggested below.  Read Aleph One's paper
carefully, in particular.

To understand what's going on, it is helpful to run code through gdb.
See the GDB tips section below.

Make sure that your exploits work within the provided virtual machine.

Start early! Theoretical knowledge of exploits does not readily
translate into the ability to write working exploits. Target1 is
relatively simple and the other problems are quite challenging.

## GDB tips

Notice the `disassemble` and `stepi` commands.

You may find the `x` command useful to examine memory (and the
different ways you can print the contents such as `/a` `/i`
after `x`). The `info register` command is helpful in printing
out the contents of registers such as `ebp` and `esp`.

A useful way to run gdb is to use the `-e` and `-s` command line flags;
for example, the command `gdb -e sploit3 -s /tmp/target3` in the VM
tells gdb to execute `sploit3` and use the symbol file in `target3`.
These flags let you trace the execution of the `target3` after the
sploit's memory image has been replaced with the target's through the
execve system call.

When running gdb using these command line flags, you should follow
the following procedure for setting breakpoints and debugging memory:

1. Tell gdb to notify you on exec(), by issuing the command `catch
   exec`
2. Run the program.  gdb will execute the sploit until the execve
   syscall, then return control to you
3. Set any breakpoints you want in the target
4. Resume execution by telling gdb `continue` (or just `c`).

If you try to set breakpoints before the exec boundary, you will
get a segfault.

If you wish, you can instrument the target code with arbitrary
assembly using the __asm__ () pseudofunction, to help with debugging.
Be sure, however, that your final exploits work against the unmodified
targets, since these we will use these in grading.

## Warnings 

Aleph One gives code that calculates addresses on the target's stack
based on addresses on the exploit's stack.  Addresses on the exploit's
stack can change based on how the exploit is executed (working
directory, arguments, environment, etc.); in my testing, I do not
guarantee to execute your exploits exactly the same way bash does.

You must therefore hard-code target stack locations in your exploits.
You should *not* use a function such as get_sp() in the exploits you
hand in.

(In other words, during grading the exploits may be run with a different
environment and different working directory than one would get by
logging in as user, changing directory to `~/hw1/sploits`, and running
`./sploit1`, etc.; your exploits must work even so.)

Your exploit programs should not take any command-line arguments.

## Collaboration Policy

You are encouraged to use the internet, the Piazza discussion board for this class, and 
classmates for information about tools, reference material, 
VM setup, and gdb. Please don't discuss solution specifics with anyone beyond your 
project partner.

## Deliverables

You will have three deliverables.

1. You will need to submit the source code for your exploits (sploit1 through sploit4 or sploit5), along
    with any files (Makefile, shellcode.h) necessary for building
    them.

1. Along with each exploit, include a text
    file (sploit1.txt, sploit2.txt, and so on). In this text file,
    explain how your exploit works: what the bug is in the
    corresponding target, how you exploit it, and where the various
    constants in your exploit come from.

1. Finally, you must include file called ID which contains, on each line: 
   UW ID Lastname, Firstname. Two lines, one for each group member. An example:
```
$ cat ./ID
9064562678 Doe, Jane
9038454624 Dough, John
$
```

Put all these files in the 'sploits/' directory and package them into a tarball with the following command (where UWID1/2 are your groups UWID #s):
```
tar -cf UWID1_UWID2_hw1.tar sploits/*
```

Upload `UWID1_UWID2_hw1.tar` to the hw-1 folder on the Desire2Learn website for this course:
https://uwmad.courses.wisconsin.edu/d2l/home/3199130

Use Assignments > Dropbox to find the hw-1 submission folder.

## Grading 

You will receive two points for each exploit that yields a root shell in our testing. 
The extra credit target, target5, is also worth two points.

There will not normally be partial credit,
but we may make an exception depending on your explanatory writeup.
We may also ask you to explain to us how and why each exploit works.
Be sure that each group member understands every exploit you turn in!


## Suggested reading in Phrack, www.phrack.org

- Aleph One, *Smashing the Stack for Fun and Profit*, Phrack 49 #14.
- klog, *The Frame Pointer Overwrite,* Phrack 55 #08.
- Bulba and Kil3r, *Bypassing StackGuard and StackShield*, Phrack 56 #0x05.
- Silvio Cesare, *Shared Library Call Redirection via ELF PLT Infection,* Phrack 56 #0x07.
- Michel Kaempf, *Vudo - An Object Superstitiously Believed to Embody Magical Powers,* Phrack 57 #0x08.
- Anonymous, *Once Upon a free()...,* Phrack 57 #0x09.
- Gera and Riq, *Advances in Format String Exploiting,* Phrack 59 #0x04.
- blexim, *Basic Integer Overflows,* Phrack 60 #0x10.

## Acknowledgements
This assignment is based in part on materials from Prof. Hovav Shacham at UC San Diego as well as Prof. Dan Boneh at Stanford. Thanks for their hard work.
