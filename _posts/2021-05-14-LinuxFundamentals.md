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

A link is basically like a shortcut which you can see in other operating systems. But on Linux, it's a little bit different. 

There are 2 type of links. **hard link** and **symbolic link** (or soft link). 

To understand link, you should know about `inode`. Every file has an inode, and one inode only. In the inode you find all the administration of a file. if you type ls -l you see a lot of information that is the information that is stored in the inode. From the inode, you go to the blocks. The blocks which is the file information that is physically stored on disc. Now in order to get to an inode, for us human beings, we give a name to a file. And the idea is you have a name somewhere in the directory, and this name points to the inode. You can have more than one name on the same inode. So you have name1 and name2 which both can point to the same inode. That's what we call a **hard link**. There is no difference between name1 and name2. So there's no relation between the original one and the link. Both are directly going to the inode. There is no hierarchical difference. No matter which one you remove, the other one will survive. Because both of them use the inode to go to the blocks. So if you write to the file using name1, then you will see that name2 will see the changes as well.

The **symbolic link** is a little bit different. Let’s say sym1, a name that points to a name1. Here youthere is a hierarchical difference. Because the symbolic link doesn't go the inode directly, the symbolic go to a hard link. And if this hard link would be removed then the symbolic links become invalid and don't work anymore. 
Hard link has two limitations. 1. There is no cross device. 2. hard links cannot be created on directories. So hard links can only be two files, not two directories.


`touch /tmp/name1`
`ls -li name1`
`903349 -rw-rw-r--. 1 user1 user1 0 May 16 18:04 name1`



Here, we created a new file 'name1'. ls -li is listing the inode number of the file and also the link counter which is 1, as no link is created yet
Now, let's create a hard link.

`ln name1 name2`

`ls -li name1 name2`

`903349 -rw-rw-r--. 2 user1 user1 0 May 16 18:04 name1`

`903349 -rw-rw-r--. 2 user1 user1 0 May 16 18:04 name2`


As you can see inode number is same for both the files

Create a symlink

`ln -s name1 sym1`

`ls -li name1 sym1`

`903349 -rw-rw-r--. 2 user1 user1 0 May 16 18:04 name1`

`903351 lrwxrwxrwx. 1 user1 user1 5 May 16 18:17 sym1 -> name1`


### Finding files with find

`find <path> -name <file-name>`

`find <path> -user <username>` find all files created by a user

`mkdir /root/any; find / -user any -exec cp {} /root/any \;`

{} -> contains output of first command when used with exec

\; -> exec should always end with \;

`find / -size +100m`

`find /-size +100M 2>/dev/null`

### Using advance `find` options

`find / -type -f -size +100M`

`find /etc -exec grep -l any {} \; -exec cp {} root/any/ \;`

`find /etc -name ‘*’ -type f | xargs grep “127.0.0.1”`

Here, xargs is used along with pipe(|) to get the output line by line
Xargs is similar to -exec


### Archiving files with `tar`

`tar -cvf my_archive.tar /home`

`tar -xvf my_archive.tar` it will be extracted to the current directory. Use -C to switch the output path

`tar -tvf` to show the content

Dd if=/dev/zero of=bigfile1 bs=1M count=1024
Dd -> copy block devices
Here, we are copying block device /dev/zero to generate zeros
of -> output file


### Managing file compression

using gzip utility:

`gzip <filename>`

`gunzip <file.gz>` To uncompress

`time <command>` to see how much time a command is taking

using bzip2 utility:

`bzip2 <filename>`

using zip utility:

`zip <filename>`

`file <filename>` command will analayze file metadata and tell what type of file it is

`tree` command will show the file hierarchy 

## Working with Text Files

### Understanding `vi`

`o` open a new line

`v` visualize. Allows you to make a block (select multiple lines)

`d` delete a block (cut)

`p` paste

`y` copy

`dd` delete a line

`u` undo

`/<string>` search

`gg`  to go to top

`<shift> g (or G)` go to end

`:%s/<string>/<new_string>/` search and replace only 1st occurance

`:%s/<string>/<new_string>/g` search and replace all occurance

`vimtutor` to learn more about vi or vim

### Browsing text files with `more` and `less`

**more** and **less** are filters for paging through text one screenful at a time. Both does similar job. But **less** is more powerful than **more**. So it is recommanded to use **less**

`more <filename>`

enter space bar to scroll down. You cannot scroll up when using more

q to quit

`less <filename>`

here, you can scroll up and down. It’s more intitutive
 

### Using `head` and `tail` to see file start and end

`head <file>` display only first 10 lines

`head -n 5 <file>` display first 5 lines

`tail <file>` display last 10 lines

`tail -n 1 <file>` display last line

`head -n 5 <file> | tail -n 1` Display 5th line

`tail -f <file>` freshen option. realtime display

### Displaying file contents with `cat` and `tac`

`cat -A <filename>` Display non printable characters for ex: new line($), space etc

`cat -b <filename>` Display line numbers skipping empty lines

`cat -n <filename>` Display line nos including empty lines

`cat -s <filename>` Remove repeated empty lines

`tac <filename` Display lines in opposite order

### Working with `grep`

`ps aux | grep ssh`

`grep any /etc*` Any files containing ‘any’ in /etc -> it will return file name & the line containing the search

`grep -i <search_string> <file>` case insensitive

`grep -v` to exclude search

for ex: `ps aux | grep ssh | grep -v grep` -> exclude grep

`grep -R` Recursive

`grep -l` Just print the file name not the line nos

`grep -A3` Print the line containing searched string + 3 lines after that

`grep -B3` Print the line containing searched string + 3 lines before that

### Understanding regular expression
`^` begining of the line
`$` end of the line
`\<` begining of a word
`\>` end of a word
`\A` start of a file
`\Z` end of a file
`{n}` exact n times
`{n,}` minimal n times
`{,n}` n times max
`{n,o}` between n and o times
`*` zero or more times
`+` one or more times
`?` zero or one time

### Using regular expression with `grep`
`egrep` extended grep
some of the search function doesn’t work with **grep** for which you can use **egrep**
for ex: `man -k user | grep 1|8`

here, `|` should work as `or` operator, but grep doesn’t understand it. for that use can use egrep
`man -k user | egrep 1|8`

similarly, `egrep ‘ab{2}c’ <filename>`

`egrep ‘(123)’ <filename>` search the block of 123

### Using common text processing utilities
**cut** filter output from a text file
**sort** sort files, often used in pipes
**tr** translates uppercase to lowercase
**awk** search for specific pattern
**sed** powerful stream editor to batch modify text files

`cut -d : -f 1 /etc/passwd` filter field no 1 (here the delimeter is :)
`cut -d : -f 1 /etc/passwd | sort`
`echo hello | tr [:lower:] [:upper:]`

`sed -n 5p /etc/passwd` printing line no 5
`sed -i` interactive
`sed -i s/how/HOW/g <filename>`
`sed -e` edit
`sed -i -e ‘2d’ <filename>` Delete line no 2

`awk -F : ‘{ print $4 }’ /etc/passwd` print 4th coloumn 
`sort -n` for numeric sorting
`awk -F : ‘/any/ { print $4 }’ /etc/passwd` print 4th coloumn that contains ‘any'

`head -n 5 /etc/passwd | tail -n 1` print 5th line
`sed -n 5p /etc/passwd` print 5th line
`ps aux | awk ‘{ print $1}’` print 1st field/coloumn
`cd /etc; grep -l ‘^root’ * 2>/dev/null` print only the file names (not the searched string) which has ‘root’ in the line beginning
`grep ‘^…$' * 2>/dev/null` search files with lines containining only 3 characters
`grep ‘\<alex\>’ <filename>` search for a specific word. search for ‘alex’ not Alexander


### Using `su`
**su** switch user. run a command with substitute user and group ID
`su -` switch to **root** and change the shell to root's shell (current directory is home directory of root)
`su` switch to root, but don't change the shell (i.e. root's environment variables will not be available in the current shell though you switched to 'root' user)
`su - user1` switch to user1 and change the shell to user1's shell
`su user1` switch to user1, but don't change the shell

### Using `sudo`
`sudo <command>` Execute the command as the super user or another user
For any user to run `sudo`, user must be member of **wheel** group (in centos) or **admin** group in (Ubuntu). Otherwise, you would get error like `user is not in the sudoers file. This incident will be reported`
To add a user to wheel group, switch to root user and run,
`usermod -aG wheel <user>`
(you need to logout and login to take this change in to effect or you can run `newgrp -`) 

When multiple commands needs to be run sudo, you can use:
`sudo -i`
This will ask for the current user's password (not the root password). If user has the sudo previlage, root shell will be initialized. Then you can run commands without sudo.

In Ubuntu, root user doesn't have password. So, you cannot use `su`. The only way to switch user & run commands is `sudo -i`


### Creating simple sudo configuration
sudo configuration is stored in `/etc/sudoers` which you should never edit manually
You can use the command `sudo visudo` to edit or update this file
For example, this file contains a line,
%wheel ALL=(ALL)    ALL
This line says, for **wheel** group, ALL hosts, ALL commands, ALL users

Similarly, we can set different rule for different user/group in the sudoers file. For ex:
`user1 AAL=/bin/passwd` 
Here, user1 is allowed change his password, nothing else

We can create snapin file under /etc/sudoers.d dir & create you own configuration
For ex: create a file `/etc/sudoers.d/ansible` and add the following configuration
`ansible ALL=(ALL) NOPASSWD: ALL`
With this configuration, 'ansible' user will be able to run sudo command without entering the password


## Connecting to a Server

### Using `SSH` to connect to remote server
`chvt` Change virtual terminal
In Linux there are 6 terminals available. Alt+F2, Alt+F3, Alt+F4 etc can be used to toggle between different terminals. terminal 1 is a graphical mode. Rest all are text terminals.
For ex: `chvt 2`

`ssh`
`ssh-keygen` 
`ssh-copy-id`
`scp`
`who` shows which terminals are used

## Working with Bash Shell

### Understanding the shell and other core linux components
Bash(Shell) -> SysCalls -> Kernel -> Hardware

### Using I/O redirection and piping
s`ort < <file_name>`
`ps aux | tee psfile` tee command is used to redirect output to a file as well as to console




### Working with `history`
History of all command is written to `~/.bash_history`
`history -c` clear the current history (from in-memory)
`histrory -w` write the current history from in memory to history file. In memory hostory will be deleted
`Ctrl-R` for reverse-i-search
`!nn` to repeat a specific line from history

### Using command line completion
`yum install bash-completion` install bash completion package if not already installed

### Using variables
`variable_name=value`
`echo $variable_name` 
By default, variables are only known to the current shell. To make it available to the sub shells, use **export**
`export variable_name=value`
`env` to list all environement variables

### Using other bash features
`Ctr-l` clear screen
`Ctrl-u` wipe out current command line
`Ctr-a` move to begining of a line
`Ctrl-e` move to end of line
`Ctrl-c` interrupt current process
`Ctrl-d` exit
`Ctrl-v` end of file sign. can be used to close the editor

**Alias** set command alias (set through /etc/profile file)
for ex: `alias help=man` with this, can use help command i.s.o man to get the manual pages
`alias egrep=‘egrep —color=auto’`
`alias fgrep=‘fgrep —color=auto’`
`alias l.=‘ls -d .* —color=auto’`
`alias ll=‘ls -l —color=auto’`
`alias ls=‘ls —color=auto’`
`alias mv=‘mv -i’`
`alias rm=‘rm -i’`
`alias which=‘alias | /usr/bin/which —ty-only —read-alias —show`
If you set alias or variables (including export) this in a shell, it will not be permanent. To set it permanenently you can use bash startup files.

### Working with Bash startup files
`/etc/environment` contains list of variables. this file is processed while starting bash. This is universal.
`/etc/profile` this is for all users (not for processes).Execute while users login
`/etc/profile.d` snappin dir contains additional configuration
`~/.bash_profile` user specific version
`~/.bash_logout` processed when user logsout. can be be used for cleanup tasks
`/etc/bashrc` processed everytime a subshell is started
`~/.bashrc` user specific one for sub shell

You can set aliases or export variables in the above confign files

Aliases are available in subshell also, but not the variables. Use ‘export’ to make variables available everywhere
`/etc/Profile.d` is a directory & you can create .sh files & set your variables or aliases.


## User and Group Management

### Understanding file ownership
```[user1@localhost log]$ cd /tmp
[user1@localhost tmp]$ touch my_file
[user1@localhost tmp]$ ls -l my_file 
-rw-rw-r--. 1 user1 user1 0 May 16 21:14 my_file
```
As you can see, file is owned by 'user1' and the group owner is 'user1'. i.e The user who created became owner of the file and the primary group of that user will become group owner of that file.
A default file permission is set. Typically, for files it is 664 (read/write for user & group. For others read) and for directory it is 775

### Creating users with `useradd`
`useradd` or `adduser` Creates a user
for ex: `useradd -C “my user” -G wheel -s /bin/passwd user1`
here -s is setting the default shell to /bin/passwd, -G is adding the user to wheel group & hence giving sudo access
As every time user ‘user1’ tries to login, it will prompt for changing the password. we can only do that with this user
Note: we can set password while creating user but we need to pass encrypted password only. so it is useless

`adduser` in Ubuntu, will create a user & ask for password. It is interactive

### Creating groups with `groupadd`
`groupadd <groupname>` create group name

### Managing users and groups properties
`w` will list who logged in to the host
`who` is shorter version of w
`getent` is used to get information from administrative databases
`getent passwd user1` here administrative database is passwd and it is fetching info about a user called ‘user1’
**/etc/shadow** is the main configuration file for user accounts
**usermod** to modifiy user account
for ex: `useradd -aG wheel user1` will add user1 to wheel group
Here, a -> is to append to existing set of groups

id <username>: to list the user & group details

`groupmod` to modify group properties
`userdel <username>`
`groupdel <groupname>`

### Configuring defaults for new users
`useradd -D` will list the defaults for a user
**/etc/login.defs** is used as the default configuration file for users. For example, password age etc.
**/etc/default/useradd** this is another configuration file 
**/etc/skell** is a directory with files which will be copied to user’s home directory when a new user is created. As a admin we can create files here which needs to be copied to every user’s home 

### Managing password properties
`passwd -l` lock the account
`password -u` unlock the account
`password -e` expire the password
`password -x` to set maximum password lifetime
`Password -n` to set minimum password lifettime
`password -S` will show password status for a user like what is the age, locked or unlocked etc
`echo <password> | passwd —stdin <username>`
`history -c` to clean the history
`chage <username>` to change the age of a user. it is interactive

### Understanding users and group configuration files
`/etc/group` contains group information.
`/etc/passwd` traditionally used to contain user & password info. Since it is readable to everyone, password info is now available in which **/etc/shadow**
to edit **/etc/passwd** file(to add new user), you can use **vipw** command

### Managing current sessions
**loginctl** allows current session management (alternate to w & who). But it can do many more stuffs.
`loginctl list-sessions` different sessions that are currently opened
`loginctl show-session <id>`
`loginctl show-user <username>`
`loginctl terminate-session <session-id>`
`loginctl session-status <session-id>`



## Permissions Management

### Understanding and managing basic Linux permissions
**chown** change user owner of a file
For ex: 
`chown user1 file1`
`chown user1:user1 file1` here group ownership is also set

**chgrp** change group owner of a file
`chgrp group1 file1`

**chmod** change permission of a file
File permission is represented in 3 or 4 bit number. For example: 775 
Here, 7 means read, write & execute, 5 means read & execute.

read - 4, write - 2, execute - 1

`chmod 770 file1`
`chmod g-w file1` This is relative methos of setting permission. Here, g-w means, takeout write permssion from group owner

In directories, these permission settings tranlates to,
read - 4 - list files
write - 2 - create/delete file
execute - 1 - change directory (cd)


### Understanding and managing advance Linux permissions
When chmod is used with 4 digits, 1st digit is for **suid+guild+sticky bit**
suid-4, guid-2, sticky bit -1

**suid** when suid is set, file will be run as owner of the file irrespective of who runs it
to set suid, you can use `chmod u+s <filename>`

`find / -perm /4000` find all files with suid(set user id) set.
for example, /usr/bin/passwd file is set with 4000 (rwsr,r-x,r-x) with user as root & group as root. When a normal user runs passwd command, it will run as root.

**sgid**: This is useful in shared user environement. when set on directory, any new files created by a user will have group owner same as the group owner of the directory. By default, group owner of a file will be the primary group of the user who created the file (typically primary group will be same as user name). In shared environment, it will be a problem because other user will not be able to write/have required permissions.
`chmod g+s <directory_name>` to set sgid

**sticky bit**: when this is set, only the directory owner or the file owner can delete the files even though users have file removal permission.
`chmod +t <directory_name>` to set stick bit
directory permission would look like: drwxrws—T 
here T represents that, sticky bit is set
s in the middle represents sgid is set 
if s is set at the beginning, it says suid is set

### Managing `umask`
**Umask** is used to set the default permission for both files and directories. It’s a common setting for both files and directories.
umask is a shell setting that defines a mask that will be subtracted from the default permission.
default permission on directories are 777
default permission on files are 666
for ex: if you run, `umask 022` Any new files you create will have permission as 644
if you run `umask 027` Any new directories you create will have as 750

Umask-> if you simply run ‘umask’ it will list the current umask value
It will be stored/set in /etc/profile & bashrc files

Note: typically all system users will have userid below 199. Ordinary user’s user id will be greater than 199.

## Storage Management Essentials

### Understanding Linux storage solutions
`lsblk` to list all block devices & partitions. i.e. disks
`fdisk <diskname>` to create partition. for ex: fdisk /dev/sda
Sda1, sda2 will be like partitions
(You need to use gdsik i.s.o fdisk on Ubuntu which uses new partiioning technique GPT whereas Centos 7 & older version are still using the old techinque MBR)

### Understanding MBR and GBT partitions

### Creating file systems
After creating partition you need to create file system explicitly in Linux. **mkfs** utility is used for this.
file system types: .xfs (default file system in Red-hat), .ext4(default in Ubuntu), vfat (compatible for Windows, Linux & Mac), btrfs, rtfs(for Windows support. not really recommanded in Linux)
For ex: `mkfs.xfs /dev/sdb1`


### Mounting file systems
After file system is created, you need to mount it
`mount /dev/sdb1 /mnt`
simply running  `mount` will list all the mounted devices

`umount /mnt` Unmount the mount
`lsof <path>` will show you opened files/processes

`findmnt` to list the mounts, where it is mounted

`!mo` will list the commands which starts with ‘mo’ from history



## Managing Networking

### Understanding IPV4 basics

### Understanding IPv6 basics

### Applying runtime network configuration

### Understanding network device naming

### Managing hostnames
`/etc/resolv.conf` will contain dns server details

`dhclient` to reach dhcp server and re-obtain the ip

`biosdevname` reveals info about network device location

`hostname -I` shows the IP addresses assigned currently to the current host
`/etc/hostname` will have host name

`hostnamectl status` about the host (similar to uname -a)
`hostnamectl set-hostname` set hostname

if no DNS is configured, you can use local alternative, /etc/hosts
here you can have ip & host mapping

`/etc/nsswitch.conf` will define search order (for ex: local dns 1st & then the dns. local passwd first & then the database)

`ping -f -s <no-of-bytes> <ip>`

### Managing hostname resolutions

### Using common network tools
`netstat -tulpen` everything listnening on the current host
`ss` (socket stat) is the new alternate to netstat
for ex: `ss -tuna`

`nmap` (not installed by default. yum install nmap) network scanning utility. will show what ports are available no matter in which machine. It’s a dangerous tool to use if you are not the expert.

`dig` to check/troubleshooting dns

`ip a`
`ip a a dev tho 1.2.3.4/8`
`ip route show`
`uname`

`ss -tuna` will show the port details. but doesn’t show which service is using that.
(it will also show established connection. like any ssh connection established by someone)
`cat /etc/services` will show service & it’s port details
for ex: `gerp 22 /etc/services` will show service name for the port 22



## Managing Time

### Understanding & managing Linux time
hardware time is set in bios
system time
NTP (Network time protocol): synchronize system time from internet

### Understanding NTP protocol

### Configuring time synchronisation
`date` will show date & time
`date -s` set time
`hwclock` to synchronise hardware time with system time
`imedatectl` 
`timedatectl set-time`
`timedatectl set-timezone`
`timedatectl status`

`stratum`
`insane clock`
`iBurst`
`chronyc source` wil show ntp servers you are connected with
`ntpdate` set date from ntp servers
`ntpq` to query ntp


## Working with Systemd

### Understanding `systemd`
**systemd** is the manager of everything in Linux. It’s the first thing that is started after starting linux kernel.
To Boot linux system, first bootlooder will kick off & then bootloader will load the KERNEL & then kernel will start systemd.

systemd is responsible for starting all the services & generating login prompt.
It also starts processes.
It also manages mounts, timer, paths & much more
Items that are managed by systemd are called **units**.
Default units are in `/usr/lib/systemd/system`, custom uints are in `/etc/systemd`

### Managing `systemd` services
`systemctl -t help` shows unit types (for ex: service, socket, busname,mount,etc)
`systemctl list-unit-files` shows all installed units
`systemctl list-units` lists active units
`systemctl enable name.service` enables but doesn’t start the service
`systemctl start name.service` starts a service
`systemctl disable name.service` disable a service
`systemctl stop name.service` stop a service
`systemctl status name.service` shows the status

### Modifying service configuration
Note: Earlier `/etc/fstab` was used to manage all mounts. Now **systemctl** can handle this.
Below listed commands are the examples for configuring services:
`yum install vsftdb`install ftp server
`systemctl status vsftdb
`systemctl enable vsftdb` (to automatically start after reboot)
`systemctl start vsftdb`

`systemctl cat name.service` reads current unit configuration 
`systemctl show` shows all available configuration parameters
`systemctl edit name.service` allows you to edit service configuration
After modifying service configuration, use `systemctl daemon-reload`
`systemctl restart name.service` to restart the service

`systemctl cat vsftdbd.service` this will display the configuration file location. for ex: `/usr/lib/systemd/system/vsftdb.service` and other details

`systemctl show vsfdtb.service`
`systemctl edit vsftdb.service` it will create/edit `/etc/systemd/system/vsftdb.service.d/override.conf` to overide the original configuration in `/usr/lib/systemd/system/vsftdb.service`


### Understanding targets
A **Target** is group of services.
Some targets are isolatable, which means you can use them as a state your system should be in.
There are 4 targets available by default
  1. Emergency.target
  2. rescue.target
  3. Multi-user.target
  4. Graphical.target

`systemctl list-dependencies name.target` to see the contents and dependencies of a systemd target

`systemctl start name.target`
`systemctl isolate name.target`
`systemctl list-dependencies name.target`
`systemctl get-default`
`systemctl set-default name.target`
`systemctl list-units | grep target`



## Process Management

### Understanding Linux processes and jobs
`bg` (to send jobs to background)
`fg` (fore ground)
`<command> &` to immediately send the job to background


### Managing interactive shell jobs


### Monitoring processes with top
`top -u <user>` 
if you press `F`, you can see what fields are avaialble in top interface
you can use `<` and `>` to sort by different field
`z` to use different color
`W` will write current display settings in ~/.toprc


### Changing top display properties

### Monitoring process properties with ps
`ps aux | less`
`ps aux | grep ssh`
`ps -ef` it will show PPID also
`ps fax` it will show forest display. i.e. relation b/w processes
`ps aux —sort pmem` to sort by memory usage


### Changing process priority
`nice`
`renice`
You can use nice & renice from top interaface also

`renice -n 5 <pid>` here we are setting the priority to 5 for a running process
`nice -n -20 <command> &` this is for a new process


### Managing process with kill
Signal: is a command which is sent to a process.
for ex: SIGHUP, SIGTERM, SIGKILL

`kill <pid>`
`kill -9 <pid>` here 9 is the signal no.
When signal number 9 is passed, it will not wait for any opened files to close before killing the process.
`killall <process-name>` to kill many processes that have a same name


## Managing Software

### Installing software from source packages
`yumdownloader gedit` will download gedit rpm package
`rpm -iv` <downloaded-rpm>`
This is the old way. Here we need to download required dependencies manually before installing the intended package

**Software manager** were developed to fix the dependency problem. They do so by working with repositories.
Common software managers are yum & apt

**YUM:**
yum uses repositories that are in `/etc/yum.repos.d`
`yum install`
`yum search` doesn’t search deep enough. For that, you can use `yum provides`
`yum remove`
`yum groups list`
`yum groups install`
`yum provides`
`yum history` shows the history. allows you to undo changes
`yum list installed` list all installed packages
`yum update` update the entire system

`yum repolist` shows available repos 

GPG key is used to verify package. This will be downloaded along with the package

### Understanding software packages

### Managing libraries

### Understanding repositories

### Managing packages with `yum`
Examples:
`yum search nmap` list all packages available related to nmap
`yum install nmap-frontend`
`yum list installed nmap` will list installed nmap related packages
`yum remove nmap`
`yum search semanage`
`yum provides semanage`

`yum groups install “Compute Node”`
`yum history` will show the history with each step
`yum history undo <step-name>` to undo specific step name. for ex: yum history undo 6


### Managing packages with `apt`
`apt-get` & `apt-cache` are old utilities
apt repositories are mentioned in `/etc/apt/sources.list`
`apt search nmap`
`apt install nmap`
`apt remove nmap`

### Using `rpm`
**RPM**(redhat package manager); should not be used anymore
`dnf` Similar to yum, used in fedora. This will be a replacement for yum in future in redhat also.

rpm can be still used to work with RPM database. allows you to package queries
rpm -qf /my/file: will tell which package a file is from
`rpm -ql my package` queries the database to list package contents
To query package files instead of package contents, add ‘p’ to the options
`rpm -qpc my package.rpm` lists configuration files in a downloaded package file
`rpm -qp —scripts my package.rpm` shows scripts that may be present in a package

`rpm -qf /usr/bin/passwd`
`rpm -ql passwd`
`rpm -qc passwd`
`yum downloader nc`
`rpm -qpl <nc-package-downloaded>`

`yum list all` it will list all packages available from al repos
`yum list installed` will show only the installed packages

## Scheduling Tasks

### Understanding Linux task scheduling
cron uses **crond** daemon
`crontab -e` to edit tasks

### Scheduling tasks with `cron`
systemctl status crond
/etc/crontab
/etc/cron directory
/etc/cron.d is a directory where you can create snapin files

Suppose you want to run a script daily/weekly/monthly etc and you don’t care about the exact time, 
then you can place the script in `/etc/cron/cron.daily` or `cron.weekly` files

write a message ‘hello’ daily 4pm to syslog
`0 16 * * * logger hello`

Note: `logger` command allows to write log directly to syslog


### Using systemd timers
A newer alternative to the cron job.
create a **.timer** unit & run it using **systemctl**

`/usr/lib/systemd/system` this will contain some .timer files
timers will look for corresponding service to run.
`systemctl status <timer-name>`
for ex: `systemctl status fstrim.timer`
`systemctl enable —now fstrim.timer`

### Using `at` to schedule tasks
To run a command script only once.
At uses **atd** daemon
`atq` to query
`atrm` to remove job

For example, To create a file 5 min from now, run below commands from terminal
```at now +5min
touch /tmp/testfile
Ctrl d to close at prompt
```


## Reading Log Files

### Understanding Linux logging
**syslog** is the legacy service that takes care of logging. In modern days, it is implemented through **rsyslogd**

`systemd-journald`


### Working with `journalctl`
`journalctl` shows complete journal
`journalctl -u <unit>` shows info about specific unit (you can use tab completion)
`journalctl —dmesg` shows kernel messages
combined filters can be used like, `journalctl -u crond —since yesterday —until 9:00 -p info`
`journalctl -u sshd`
`journalctl tab tab` (to list the units by tab completion)
`systemctl status sshd` gives similar info


### Understanding `rsyslog`
/etc/rsyslog.conf: will contain rules like where speicific log types should be written. for ex: authentication related logs will be written to /var/log/secure




