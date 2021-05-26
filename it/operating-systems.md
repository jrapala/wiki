# Operating Systems

**Operating System:** The whole package tha manages our computer's resources and lets us interact with it.



**Two Parts of an Operating System:**

1. **Kernel**: The main core of an operating system. It talks directly to our hardware and manages our systems resources. Has the following functions:
   - **Process Manager**: What order do programs run in? What resources do they take? The process scheduler helps make multitasking possible.
   - **Memory Manager**: The kernel optimizes memory usage and makes sure our applications have enough memory to run.
   - **File Manager**: Every OS has a file system that organizes files
   - **I/O Manager**: How our kernel talks to external devices.
2. **User Space**: What users interact with (programs and interfaces).



**Major Operating Systems**

- Windows
  - Widely used in the business and consumer space
  - PCs now refer to Windows computers
- Mac
  - Mainly used in the consumer space
- Linux
  - An open-source OS.
  - Used in business, infrastructure, and the consumer space.
  - There are many different distributions. Common ones include Ubuntu, Debian, and Red Hat.
- Chrome OS (runs Linux kernel under the hood)
- iOS
- Android OS (runs Linux kernel under the hood)
- Windows 10 Mobile



### File Systems

Handled by the kernel.

**Three Main Components to Handling Files**

1. **File Data**: We write data to our hard drive in the form of data blocks. These can be saved to different parts of the disk.
   - **Block storage**: Improves faster handling of data because the data isn't stored as one long piece and can be accessed quicker
2. **Metadata**: Contains the information about the files (file owner, permissions, file size, date modified, date created, file type)
   - **Filed extension**: The appended part of a filename that tells us what type of file it is in certain operating systems
3. **File System**: There are different types based on the amount of storage you need, the speed, resiliancy, journaling, etc.. Each major OS manufacturer has their own unique file system:
   -  Windows uses NTFS which includes encryption, faster access speeds, security, and more
     - Microsoft is currently working on a new ReFS file system
     - MacOS used HFS+ until 2017 when it switched to APFS.
     - Different Linux distributions use different file system types. ext4 is one standard.
   - You will not be able to easily move files across file systems.



### Process Management

**Process:** A program that's executing, like our internet browser or text editor

**Program**: An application we can run, like Chrome. We can have many processes of the same program running at the same time.



The kernel manages resources efficiently, so that all programs we want to use can be run. The kernel created processes, efficiently schedules them, and manages how processes are terminated.

A process needs to be created for what a program wants to run. This process needs resources like RAM and CPU. The kernel has to schedule time for the CPU to execute instructions in the process. 



**How does a CPU execuite multiple processes at once?** It doesn't do it at once, but does it one at a time with a **time slice**: a very short interval of time that gets allocated to a process for CPU execution. 



**Reasons for a slow computer:**

- A process is taking up more time slices than it should
- There are too many processes that want CPU time and the CPU can't keep up

The kernel does a good job however sometimes we need to step in and manage processes manually.





### Memory Management

Processes need CPU time and memory. We can use more memory than we physically have.

**Virtual Memory**: The combination of hard drive space and RAM that acts like memory that our processes can use.

- When we execute a process, we take the data of the program in chunks called pages. These are stored in virtual memory. If we want to read or execute these pages, they must be sent to physical memory/RAM.
- Notice how some unused parts of an app are slow to load on the first time. This is because they were being stored in virtual memory.
- The allocated space for virtual memory on a hard drive is called **swap space**.



### I/O Management

The kernel needs to be able to load up drivers that are used so we can communicate with external hardware. It also manages the transfer of data in and out of these devices, and intercommunication between devices.



**When you're troubleshooting or solving a problem with a slow machine, it's usually some sort of hardware resource deficiency.**

- Not enough RAM = can't load as many processes
- Not enouch CPU = can't execute programs fast enough
- Too much input or output = block other data from being sent or recieved 



### User Space

You can interact with your OS with either a shell (like a CLI shell) or a GUI. When you log into a machine remotely, you often won't be given a GUI.

- **Shell**: A program that interprets text commands and send them to the OS to execute.
  - There are lots of different types of shell. Some have different features. Some handle performance differently.
  - BASH and Powershell are two popular examples



### Logs

**Logs**: Files that record system events on our computer, just like a system's diary. e.g. When it was turned on, when a driver was loaded, etc..



### The Boot Process

1. Computer is powered on
2. BIOS/UEFI starts the POST (Power On Self Test)
3. A boot device is selected. If there are multiple boot devices, they are configured in a certain order. These are checked, in order, for a **bootloader**: a small program that loads the operating system.
4. Bootloader is executed. The operating system is loaded.
5. The kernel is loaded, which allows access to computer resources and loads up drivers.
6. Essential system processes and user space items are launched (e.g. user log in, a desktop environment, etc.).  



### Mobile Operating Systems

These OSs are optimized to use as little power as possible by removing OS features and applications that a mobile device doesn't need. It also uses different drivers for the different ways you interact with a mobie device.