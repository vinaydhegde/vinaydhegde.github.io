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
**LinuxFoundation** is an organization that takes care of many Linux projects. But there are other organizations as well who take care some of Linux projects. For example, **Linux Kernel, Systemd, GNU utils** are some of these projects. All these software are available for free, and anybody can use it. So if you are very skilled you can take all this software, put it together, and build your own operating system. And that is exactly the essence of what we call distribution. So the Linux distribution is a collection of software that consists of all this open source software.
 
One big and important distribution is **RedHat** and it is the leading Linux distribution. For the simple reason that RedHat is providing enterprise supported Linux.
There are two related distributions, one of them is **CentOS**. CentOS is the community enterprise operating system, and the only difference between RedHat Enterprise Linux and CentOS is that in CentOS the proprietary information from RedHat inc. has been removed. So CentOS is a community enterprise operating system, which means it is available completely for free. And another important distribution in the RedHat family is **Fedora**. Fedora is the development platform, or the community operating system. 
The other important Linux distribution is **Ubuntu**. Ubuntu itself doesn't stand on its own. Because Ubuntu was based on a very old and respectable Linux distribution with the name **Debian**. And based on Ubuntu we have another distribution which is **Linux Mint**. 
There are many other distrubutions. Some of the important distrubutions are, **SUSE** & **Oracle Linux distrubution**



## Essential File Management Tools

### Linux File System Hierarchy
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

### Using wildcards

### Copying files with `cp`

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




