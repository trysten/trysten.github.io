---
layout: post
title: Delta-only internet backup of windows computers
categories: backup windows rsync cygwin

---

Windows clients get infected sometimes, you should back them up.

Let's use Cygwin/rsync, qemu's implementation of the NBD protocol, and maybe a few docker containers to bare-metal backup a Windows 10 computer. 

### Clone
First, I use disk2vhd to make a .vhdx of the system drive. You can just do the windows partition or the whole HD. You could even make a clone of a disk without using Disk2VHD and use that as your backup destination. I'd stick to a single disk so it's easier to physically replicate later on. 

### Cygwin
I [install cygwin][ic] on many of the Windows computers that I use. We're going to use it in this guide to securely copy files from our Windows host. I find that the default location, C:\cygwin64, is convienent. Select a mirror and setup will load a list of packages. Search for and mark _openssh_ and _rsync_ packages for installation. 
#### Cygrunsrv and sshd
Now we'll run a helper script that comes with the openssh package to install and configure sshd: `ssh-host-config`. Use caution when running this script, I have caused more than a few problems by typing too fast or killing the script before it is complete. 

[ic]: <https://cygwin.org/install.html> "Install Cygwin"

### 


# ideas: #
* install windows boot code to external boot VHD natively 

* combine those ideas to have incremental filestore with up-to-date copy on bootable drive
  * use lvm snapshots or other method to preserve history of files
  * keep latest (or mirror of latest) backup on bootable drive in it's original state
  * seperate copies are possible, but to orchestrate timing, we must keep all control in once place

* Complicated incrementals are wrong, just use rotation

* forward backup target port 22 to backup controller container 
    * mount vhd from snapshot to test
    * connect and backup files
    
