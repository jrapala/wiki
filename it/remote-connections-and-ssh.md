# Remote Connections and SSH

**Remote Connection:** Allows us to manage multiple machines from anywhere in the world.

**Secure Shell (SSH):** A protocol implemented by other programs to securely access one computer from another.



**Using SSH**

You need to have an **SSH client** installed on the computer you're connecting from, and an **SSH server** on the computer you're trying to connect to. The SSH server is just a background process that constantly checks for clients trying to connect to it, and then will authenticate it's requests.

On Linux, the most popular program to use SSH is **OpenSSH**.

On Windows, the most popular program to use SSH is **PuTTY**.



**Logging Into a Remote Machine**

You'll need the computer account, and the host name/IP address of the remote computer.

```
$ ssh juliette@104.131.122.215
The authenticity of host '104.131.122.215 (104.131.122.215)' can't be established.
ECDSA key fingerpreint is SHA256:463jd781238asf2935
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '104.131.122.215' (ECDSA) to the list of known hosts.
juliette@104.131.122.215's password: 
```

"The authenticity of host.." means we've never connected to this machine before, so our SSH client can't verify we're connecting to a machine we want to connect to.

After typing in your password, you are connected. Any text commands are sent securely to the SSH server. You can even launch an application and see the GUI.



**Passwords vs SSH Authentication Keys**

Keys are more secure than passwords.

They come in sets of **private** and **public** keys. Only those with both can access the data.

**Public Key:** You can lock something, but you can't unlock it.

**Private Key:** You can unlock something, but you can't lock it.



**VPN (Virtual Private Network)**

Allows you to connect to a private network, like your work network, over the Internet.

Lets you access actual resources like shared file servers and network devices as if you are connected to your work network.



**Remote Connections on Windows (PuTTY GUI)**

1. Launch GUI
2. Set Host Name/IP address, Port (default port SSH uses is 22), and Connection Type (SSH)
3. Click Open. A terminal will launch.
4. Type your username and password.



**Remote Connections on Windows (PuTTY and Power Shell)**

1. Open Power Shell
2. Type `putty.exe -ssh juliette@104.131.122.215`
3. A terminal will launch. Type in your password.



**Windows - Remote Desktop Protocol (RDP) **

A way to connect to other Windows computers. There are RDP clients for Linux and OS X  too.

A client program called the **Microsoft Terminal Services Client (mstsc.exe)** is used to create RDP connections to remote computers.



**Enabling Remote Connections on Windows**

1. Open Start Menu
2. Right click on This PC
3. Select Properties
4. Select Remote settings
5. Pick an option from the Remote Desktop section
6. If you're on a list of allowed users, you can use mstsc.exe to connect to it from anywhere on the network.