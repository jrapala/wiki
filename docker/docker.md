# Docker

## What is Docker?
---
Docker is a platform for building, running and shipping applications in a consistent manner.
### Why do some things work on one machine but not others?
- One or more files are missing
- Software version mismatch (e.g. node versions)
- Different configuration settings (e.g. env vars)
### Benefits
- Consistent builds, runs and ships
- Easy cleanup!

## Virtual Machines vs Containers
----
**Containers:** An isolated environment for running an applications. Includes all dependencies and configurations. Highly portable.
**Virtual Machines:** A abstraction of a machine (physical hardware). e.g. A Mac can run a Linux and Windows VM at the same time.

A hypervisor tool can help you create and manager virtual machines.
- VirtualBox (cross-platform)
- VMware (cross-platform)
- Hyper-v (Windows only)

### Problems with Virtual Machines
- Each VM needs a full-blown OS (OS kernel + application layer)
- Can be slow to start
- Resource intensive

### Benefits of Containers
- Allow running multiple apps in isolation
- Are lightweight
- Use OS kernel of the host (only virtualizes the application layer)
	- However this means that Docker images will only be able to run if it is compatible with the OS kernel
		- Docker Toolbox can help you if you are using an incompatible OS
- Starts quickly
- Needs less hardware resources

## Container Repositories
---
Containers live in a **container repository**, so they can be shared with others.
Many companies have their own private repositories.
Docker has a public repository called Docker Hub.

## Docker Architecture
---
Docker has a client/server (Docker engine) architecture that communicate with a REST API.

A container is just another process running on your computer.

All containers share the kernel of the host. A kernel manages all applications and hardware resources. Which is why you can't run a Windows application on Linux.

### Who can use what?
- Windows 10+: Can run Windows and Linux (comes with Linux kernel)
- Linux: Can run Linux
- MacOS: No kernel support. So Docker on Mac runs a lightweight Linux VM instead,

## Installing Docker
---
Download Docker Desktop: https://docs.docker.com/get-docker

Don't forget to start the app!

## Development Workflow
---
### Dockerize an Application
Add a **Dockerfile**. This includes information on how to package up an application into an image.

An **image** includes everything the application needs to run, such as:
- A cut-down OS (usually a small Linux base image, like alpine)
- A runtime environment (e.g. Node)
- Application files
- Third-party libraries
- Environment variables

Once you have an image, Docker will start a container with that image.
There will be a port binded to it, which makes it possible to talk to the application.
A container could contains layers of images.

### Images vs Containers

If it's not running, it's an image.
If it's running, it's a container.

The **container** is the running environment for the **image**. It has it's own abstraction of an operating system.

### Image Tags

Images can have tags, which correspond to versions.
e.g. Repository: postgres, Tag: 9.6.5

### Docker Hub
Once you have an image, you can push it to a Docker registry (Docker Hub).
Everything on Docker Hub is an image.
You can pull these images onto any machine.

`$ docker run postgres:9.6

### Step-By-Step
To Dockerize a simple JS application (in `app.js`):

Dockerfile:
```dockerfile
# Found on Docker Hub
# There are many node images, this one is based on a lightweight linux diistribution

FROM node:alpine

# Copy all files into a new directory on the image called /app
COPY . /app

# Switch into the /app directory. If you didn't do this, the next step would be CMD node /app/app.js
WORKDIR /app

CMD node app.js
```

**To build the image:** `$ docker build -t hello-docker .` (Build something tagged hello-docker in the current directory)

**To see all images:** `$ docker image ls`

**To run the image:** `$ docker run hello-docker`
This starts the image in a container.

### Play with Docker
One way to learn Docker: https://labs.play-with-docker.com

Only has access to Linux and Docker (i.e. node is not installed). You can use an image from Docker Hub to get node.

```bash
$ docker pull codewithmosh/hello-docker
$ docker image ls
REPOSITORY                  TAG       IMAGE ID       CREATED       SIZE
codewithmosh/hello-docker   latest    fd84d4d9cb44   2 years ago   112MB
$ docker run codewithmosh/hello-docker
Hello World!
```

Docker Containers (Using Linux)
---
### Distributions
Different versions of Linux exist for different needs.

**Examples:**
- Ubuntu (popular)
- Debian
- Alpine (very small)
- Fedora
- CentOS

**Pulling a new image:** `$ docker pull ubuntu`
This is not necessary though. Docker will pull an image if you try to run one and don't have it installed.

**Running a container:** `$ docker run unbuntu`
This will run and immediately stop.

**Running a container (detached mode):** `$ docker run -d ubuntu`
The container will not stop.

**View running containers:** `$ docker ps`

**View all containers (including stopped):** `$ docker ps -a`

**Run a container and interact with it:** `$ docker run -it ubuntu`
This will output a shell prompt like `root@2f759e6996e9:/#` (user@machine-name:current-dir_privilege) (# for highest user or $ for normal user)

**Stop a container:** `$ docker stop <id-number>`

**Start a container by id:** `$ docker start <id-number>`

## Linux Packages
---
The package manage on Linux is `apt` (advanced package tool).
`apt-get` is also popular. `apt` is newer and more user friendly.

**To see packages available in current package database:** `apt list`

**To update package database**: `apt update` (always run this first)

**To install a package:**  `apt install nano` (a basic text editor)

**To remove a package:** `apt remove nano`

## Linux File System
---

```
/
  bin (binaries/program files - include programs like pwd)
  boot (boot files)
  dev (device files)
  etc ("etsy" - editable text configurations)
  home (home dirs for users)
  root (home dir for root user)
  lib (library files)
  var (variable - frequently updated files like logs)
  proc (files for running processes)
```

### Navigation

**Current dir**: `$ pwd` (print working directory)

**List items in dir:** `$ ls`
**One item per line:** `$ ls -1`
**A long list:** `$ ls -l`

**Change directory:** `$ cd (relative or absolute path)`
**Root directory**: `/`
**Home directory:** `~`

### Modifying Files

**Move a file:** `$ mv hello.txt /etc`
**Rename a file:** `$ mv hello.txt hello-docker.txt`
**Remove a file:** `$ rm file*` (remove all files that start with "file")
**Remove a directory:** `$ rm -r docker/` (must use `-r` recursive flag)

### Reading Files

**View contents of a file:** `$ cat file.txt`
**View contents of a long file:** `$ more long-file.txt` (gives you pagination via space key or enter for one line at a time)
**View contents of a long file with scroll up option:** `$ less long-file.txt` (may need to install) (up/down/space/enter)
**View first few lines of a file:** `$ head -n 5 long-file.txt` (see first 5 lines)
**View last few lines of a file:** `$ tail -n 5 long-file.txt` (see last 5 lines)

### Redirection

`$ cat file.txt` uses standard output.

**To redirect output:** `$ cat file1.txt > file2.txt` (read file1 and write to file2)
`$ cat file1.txt file2.txt > file3.txt` (write two files to file3)

**To write a single line to a file**: `$ echo hello > hello.txt`

**To write directory items to a file:** `$ ls -l /etc > files.text`

