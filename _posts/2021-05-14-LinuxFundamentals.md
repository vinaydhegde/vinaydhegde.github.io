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
`mkdir /tmp/test` will create 'test' dir in /tmp
`mkdir /tmp/test1/test2` would complain `mkdir: cannot create directory '/tmp/test1/test2': No such file or directory`
`mkdir -p /tmp/test1/test2` will create test1 and test2 dir.
`rmdir /tmp/test` will remove 'test' dir, only if it is empty
`rm -rf /tmp/test` will remove 'test' dir and it's contents

### Using absolute and relative paths
Let's say, current dir is /temp/test/test1
`cd ../test2` will switch the current directory to /temp/test/test2. `..` is used to refer one folder up. This is relative path.
`cd /temp/test/test2` will switch the current directory to /temp/test/test2. This is absolute path

### Moving files with `mv`
mv command is used to move file/dir (cut and paste) and also rename a file/dir
let's say, /tmp/test is a directory with some files in it.
`mv /tmp/test /temp/some-other-dir/test` will move 'test' directory and it's contents to 'some-other-dir'
`mv /tmp/test /temp/test-test` will rename 'test' dir to 'test-test' 

### Removing files with `rm`
`rm /tmp/file1` will remove file1
`rm -rf /temp/test` will remove test directory and it's contents

### Understanding HardLinks and SymbolicLinks
A link is basically like a shortcut which you can see in other operating systems. But on Linux, it's a little bit different. There are 2 type of links. **hard link** and **symbolic link** (or soft link). 
To understand link, you should know about `inode`. Every file has an inode, and one inode only. In the inode you find all the administration of a file. if you type ls -l you see a lot of information that is the information that is stored in the inode. From the inode, you go to the blocks. The blocks which is the file information that is physically stored on disc. Now in order to get to an inode, for us human beings, we give a name to a file. And the idea is you have a name somewhere in the directory, and this name points to the inode. You can have more than one name on the same inode. So you have name1 and name2 which both can point to the same inode. That's what we call a **hard link**. There is no difference between name1 and name2. So there's no relation between the original one and the link. Both are directly going to the inode. There is no hierarchical difference. No matter which one you remove, the other one will survive. Because both of them use the inode to go to the blocks. So if you write to the file using name1, then you will see that name2 will see the changes as well.

The **symbolic link** is a little bit different. Let’s say sym1, a name that points to a name1. Here youthere is a hierarchical difference. Because the symbolic link doesn't go the inode directly, the symbolic go to a hard link. And if this hard link would be removed then the symbolic links become invalid and don't work anymore. 
Hard link has two limitations. 1. There is no cross device. 2. hard links cannot be created on directories. So hard links can only be two files, not two directories.

```[user1@localhost tmp]$ touch /tmp/name1
[user1@localhost tmp]$ ls -li name1
903349 -rw-rw-r--. 1 user1 user1 0 May 16 18:04 name1
```
Here, we created a new file 'name1'. ls -li is listing the inode number of the file and also the link counter which is 1, as no link is created yet
Now, let's create a hard link.
```[user1@localhost tmp]$ ln name1 name2

[user1@localhost tmp]$ ls -li name1 name2
903349 -rw-rw-r--. 2 user1 user1 0 May 16 18:04 name1
903349 -rw-rw-r--. 2 user1 user1 0 May 16 18:04 name2
```
As you can see inode number is same for both the files

Create a symlink
```[user1@localhost tmp]$ ln -s name1 sym1

[user1@localhost tmp]$ ls -li name1 sym1
903349 -rw-rw-r--. 2 user1 user1 0 May 16 18:04 name1
903351 lrwxrwxrwx. 1 user1 user1 5 May 16 18:17 sym1 -> name1
```

### Finding files with `find`
find <path> -name <file-name>
find <path> -user <username>: find all files created by a user
Mkdir /root/any; find / -user any -exec cp {} /root/any \;
{} -> contains output of first command when used with exec
\; -> exec should always end with \;
find / -size +100m
Find /-size +100M 2>/dev/null

Find / -type -f -size +100M
find /etc -exec grep -l any {} \; -exec cp {} root/any/ \;
find /etc -name ‘*’ -type f | xargs grep “127.0.0.1”
Here, xargs is used along with pipe(|) to get the output line by line
Xargs is similar to -exec

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




