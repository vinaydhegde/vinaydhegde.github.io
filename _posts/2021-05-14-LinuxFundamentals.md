---
title: "Linux Fundamentals"
toc: true
toc_label: "Content"
toc_sticky: true
tags:
  - Linux Fundamentals

date: 2021-05-14
header:
  teaser: /assets/images/nature.jpg
excerpt: "This blog covers Linux fundamentals and regualrly used commands"
---
## Introduction

### Linux Distributions
**LinuxFoundation** is an organization that takes care of many Linux projects. Like Linux Foundations, there are many other organizations who take care some of Linux projects. For example, **Linux Kernel, Systemd, GNU utils** are some of these projects. All these software are available for free, and anybody can use it. So if you are very skilled you can take all this software, put it together, and build your own operating system. And that is exactly the essence of what we call distribution. So the Linux distribution is a collection of software that consists of all this open source software.
 
One of the major and leading Linux distribution is **RedHat**. For the simple reason that RedHat is providing enterprise support on Linux.
There are two related distributions. 1. **CentOS** 2. **Fedora**
CentOS is the community enterprise operating system where it doesn't contain any proprietary information which  RedHat inc. has. So it's a community enterprise OS.
Fedora is the development platform, or the community operating system.
Another important Linux distribution is **Ubuntu**. Ubuntu is based on old Linux distrubution **Debian**. Based on Ubuntu, there is one more distrubution called **Linux Mint**.
There are many other distrubutions. Viz. **SUSE** & **Oracle Linux**


## Linux File System Hierarchy
Every Linux file system starts with `/`. 

`/` is the root directory. 

`/bin` for binaries. 

`/home` for user home directories

`/var` directory, where you will find log files and more. var contains a wide variety of files that dynamically can be created by the operating system, and one of the most commonly known directories in var is var/log. In var/log, you will find log files that your system is creating. 

`/usr` It is probably one of the most important directory on your system. It's like Program Files in Windows, and you will find all of the binaries, which are divided in the subdirectory `bin` and `sbin`. bin for regular binaries, sbin for system binaries. So sbin are root commands, and bin are regular user commands.

`/boot` directory: for booting, and often, depending on your Linux configuration, the boot directory will be on a separate partition, because the files that are in the boot directory need to be accessible at all times. Linux kernel, the heart of your operating system is also located here.

`/dev` It contains device files and these files provide access to hardware. One of the device file is hard disk. Fo ex: sda1 is hard disk,  sda1 and sda2 are partitions on your hard disk.

 `/etc` is for configuration files. For ex: `os-release` is a standard configuration file that should exist on all Linux services, and it describes the current version that you are using. `password` file is one another important configuration file 
 
`/media` and `/mnt` are  for mounts, for connecting devices. Mount means that you can connect a storage device to a separate directory

`/proc` One of the most intriguing directories on your system is proc, because proc is an interface to the Linux kernel

For ex:  `cpuinfo`, providing CPU information that the kernel has directly detected from the hardware in your computer. `meminfo`, which is providing memory information.

`/root` is the home directory for the root user. 

`/run`  is a new temp directory. Processes that temporarily need to write files do that in the run directory. 

`/srv` is typically empty. It's meant for services, and you can use it to store document roots for services like a web server or an FTP server. 

`/sys` is for hardware information.  

`/tmp` is for temporary files. This is the old directory where /run is the new directory, where processis can write temporary files. 



## Using Essential tools

### Some basic commands

### Getting help with `man`


## Essential File Management Tools

### Listing files with `ls`
`ls` list files (and directories) in the current diectory
`ls -a` show hidden files also
`ls -R` list files in current directory and sub directories
`ls -l` long listing. show all fields/coloumns including permissions
`ls -lt` sort by modification time, newest first
`ls -ltr` sort by modification time, newest last
`ls -d` or `ls -ld` list directories themselves, not their contents. Useful, when you have to check permission of a directory
`ls -il` list inode or index number also
`ls -S` sort files by size

### Using wildcards
`ls *` list everything recursively
`ls a*` list everything recursively which starts with 'a'. Note: if a directory starts with 'a', all files in it will be listed.
`ls -d a?d*` list files which starts with 'a' and followed by any sigle character and then char 'd' followed by any number of characters. For ex: audit
Note: when '-d' is used, it won't search recursively
`ls -d a[nu]*`list files which starts with 'a' and followed by 'n' or 'u' and then any characters. For ex: anaconda, audit
`ls -d m[a-e]*`list files whcih startes with 'm' and second char can be either 'a' or 'b' or 'c' or 'd' or 'e' followed by any characters. For ex: maillog, messages

### Copying files with `cp`
let's say, your current directory is `/var/log` and it has directory called `anaconda` with 6 files in it. Destination is /tmp and it doesn't contain 'test' dir

When source is a directory:
`cp anaconda /tmp/test` would complain `cp: -r not specified; omitting directory 'anaconda'`
`cp anaconda/ /tmp/test` would complain `cp: -r not specified; omitting directory 'anaconda'`
`cp anaconda/* /tmp/test` would complain `cp: target '/tmp/test' is not a directory`. This means, if source is a directory, destination also should be a directory and that should exist.
Let's create 'test' direcorty now. `mkdir /tmp/test`
`cp anaconda/* /tmp/test` This will copy all 6 files to /tmp/test (but not the anaconda dir)
`cp anaconda -R /tmp/test` This will copy anaconda directory (and it's files) to /tmp/test
`cp anaconda/ -R /tmp/test` This will copy all 6 files to /tmp/test (but not the anaconda dir)

When source is a file:
Let's remove '/tmp/test' dir.
`cp anaconda/anaconda.log /tmp/test` Here, 'anaconda.log' file contents will be copied to 'test' file 
`cp anaconda/anaconda.log /tmp/test/` would complain `cp: cannot create regular file '/tmp/test/': Not a directory`
let's create '/tmp/test' dir and then run,
`cp anaconda/anaconda.log /tmp/test/` will now copy the file 'anaconda.log' to 'test' dir
`cp anaconda/anaconda.log /tmp/test/test1.log` will copy 'anaconda.log' file contents to 'test1.log'


### Working with directories

### Using absolute and relative paths

### Moving files with `mv`

### Removing files with `rm`

### Understanding HardLinks and SymbolicLinks

### Finding files with `find`

### Using advance `find` options

### Archiving files with `tar`

### Managing file compression


## Working with Text Files

### Understanding `vi`

### Creating text files with `vi`

### Browsing text files with `more` and `less`

### Using `head` and `tail` to see file start and end

### Displaying file contents with `cat` and `tac`

### Working with `grep`

### Understanding regular expression

### Using regular expression with `grep`

### Using common text processing utilities

### Using `su`

### Using `sudo`

### Creating simple sudo configuration



## Connecting to a Server

### Using `SSH` to connect to remote server


## Working with Bash Shell

### Understanding the shell and other core linux components

### Using I/O redirection and piping

### Working with `history`

### Using command line completion

### Using variables

### Using other bash features

### Working with Bash startup files


## User and Group Management

### Understanding users

### Understanding file ownership

### Creating users with `useradd`

### Creating groups with `groupadd`

### Managing users and groups properties

### Configuring defaults for new users

### Managing password properties

### Understanding users and group configuration files

### Managing current sessions


## Permissions Management

### Understanding and managing basic Linux permissions

### Understanding and managing advance Linux permissions

### Managing `umask`


## Storage Management Essentials

### Understanding Linux storage solutions

### Understanding MBR and GBT partitions

### Creating file systems

### Mounting file systems


## Managing Networking

### Understanding IPV4 basics

### Understanding IPv6 basics

### Applying runtime network configuration

### Understanding network device naming

### Managing hostnames

### Managing hostname resolutions

### Using common network tools


## Managing Time

### Understanding & managing Linux time

### Understanding NTP protocol

### Configuring time synchronisation


## Working with Systemd

### Understanding `systemd`

### Managing `systemd` services

### Modifying service configuration

### Understanding targets


## Process Management

### Understanding Linux processes and jobs

### Managing interactive shell jobs

### Monitoring processes with top

### Changing top display properties

### Monitoring process properties with ps

### Changing process priority

### Managing process with kill


## Managing Software

### Installing software from source packages

### Understanding software packages

### Managing libraries

### Understanding repositories

### Managing packages with `yum`

### Managing packages with `apt`

### Using `rpm`


## Scheduling Tasks

### Understanding Linux task scheduling

### Scheduling tasks with `cron`

### Using systemd timers

### Using `at` to schedule tasks


## Reading Log Files

### Understanding Linux logging

### Working with `journalctl`

### Understanding `rsyslog`




