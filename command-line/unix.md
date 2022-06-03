# Introduction to UNIX

There are two main groups of operating systems: **Microsoft NT** descendents (Windows, XBox OS, Windows Phone) and **UNIX** descendents (Mac OS, Android, Chrome, etc..).

**What is UNIX?** An OS developed at AT&T Bell Labs in the 1960s through the 1980s. GNU/Linux, MacOSX, and Android are all bases on ideas and specifications created by UNIX.

UNIX is a collection of programs:

 - **kernel**: A program that manages resources. It mediates access between user programs and system resources (e.g. CPU scheduling, I/O to computer hardware, and memory). Programs request resources by making a **syscall** which follows the **POSIX** standard.

 - **shell**: A program that can execute other programs from a text-based interface. You interact with a program from the command-line with text commands and text output. Most modern shells are influenced by the UNIX shell. A **terminal** is software that runs a shell. Terminal --> Shell --> OS

   **List of shells:**

   - thompson shell - 1971
   - pwb (mashey) shell - 1975
   - bourne shell - 1977
   - c shell (bsh) - 1978
   - tcsh - 1983
   - korn shell - 1983
   - bourne again shell (bash) - 1987 (most popular)
   - almquist shell (ash) - 1989
   - debian almquist shell (dash) - 1997

	- **utilities**: System tools, some you may have written. Any distribution of UNIX will come with dozens of programs that perform single-purpose tasks.



**Where can you find a UNIX command-line?** WiFi routers, DSL and cable modems, Raspberry PI, Android phones (adb), Linux computer, Mac OSX computer, web server



**The Unix Philosophy**

- Each program should do one thing well
- The output of a program can become the input of another



**Note:** UNIX is a trademark of a global consortium called The Open Group. They will certify an OS if it passes conformance tests. You must pay to be tested and use the UNIX trademark. Mac OS is now true UNIX. 

Uncertified operating systems are called Unix-like. They typically follow the rules, but are simply not certified.

Linux is a result of the Free Software Movement led by Richard Stallman. This created the GNU Project which created a free Unix system. Another developer, Linus Torvalds, created his own kernel named Linux, which was incorporated into GNU. These days, Linux refers to all of the software that is part of the Linux ecosystem.

Linus-bases operating systems are called **Linux Distributions** (Linux kernal + GNU tools + documentation + package manager + window system + desktop environment). 1000+ are available (Fedora, Ubuntu, Debian, etc..)



## Basic Commands

**Structure of a Command**

​	command + argument 

​	`$ cd ../..`

**Flags**

- A flag is a special kind of argument that starts with a `-` or `--`. Sometimes called a switch. It customizes the behavior of a command.



**What shell am I in?**  `$ echo $SHELL`

**List Files** `$ ls` 

	- `$ ls -1` for one column

**Present Working Directory** `$ pwd`

**Change Directory** `$ cd`

**More Info on a Command** `$ man ls` (or any other argument) (`q` to exit)

**Display Single Text Files** `$ cat` - You can use this on multiple files at the same time.

**Reset** `$ reset` - used if you're stuck and can't type anymore



## Command Structure

```
$ command -options arguments
```





## Aliases

Run commands in a custom way.

`$ alias ls='ls -1'` for the current window. Add to your `rc` file to save, like `~/.bashrc`. 



## Special Directories

**Root Directory:** `/`

**Home Directory:** `~` (typing just `$ cd` will bring you back home)

**Current Directory:** `.`

**Parent Directory:** `..`



## Conventions

A flag can be added before or after an argument: 

​	`$ wc -l moby-dick.txt` or `$ wc moby-dick.txt -l`

Some programs have flags that expect values

​	`$ head -n 5 moby-dick.txt` (print first 5 lines)

​	`$ head --lines=5 moby-dick.txt`