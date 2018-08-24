---
title: file permission for ubuntu
date: 2018-08-23 11:02:10
tags: ['chmod']
categories: ['chmod']
---

In Linux, everything is a file. Directories are files. All of the files on a system have permissions that allow or prevent others from viewing, modifying or executing. The super user "root" has the ability to access any file on the system. Each file has access restrictions with permissions, user restrictions with ownwe/group asscciation. Permission are referred to as bits.

To change or edit files that are owned by root, `sudo` must be uesd.

### Quick Start

If the owner read & execute bit are on, then the permission are:
```javascript
-r-x------
```

#### Access restrictions
There are three types of access restrictions:

| Permission | Action  | chmod option |
| :----: | :---: |   :---:     |
|  read      |  view   |  r or 4      |
|  write     |  edit   |   w or 2     |
|  execute   | execute  |  x or 1     |

#### user restrictions
There are alsoe three types of user restrictions:

| user | ls output  |
| :----: | :---- |
|  owner   |  -rwx------ |
|  group   |  ----rwx--- |
|  other   | -------rwx  |

### Folder/Directory Permissions
Directories have directory permissions. The directory permissions restrict different actions than with files or divice nodes.


| Permission | Action  | chmod option |
| :------: | :----- |   :----     |
|  read      |  view contents i.e: ls comman   |  r or 4      |
|  write     |  create or remove files from dir   |   w or 2     |
|  execute   |  cd into directory  |  x or 1     |

### Permissions in Action
```javascript
local@local-OptiPlex-7020:/home$ ls -l 
total 4
drwxr-xr-x 48 local local 4096 Aug 20 17:01 local
local@local-OptiPlex-7020:/home$
```

Using the example above we have the file '/home/local' which os owned bu the user root and belongs to the root group.
What are the permissions from the above 'home/local' ls output? 
```javascript
drwxr-xr-x

owner = Read & Write & Execute(rwx)
group = Read & Execute(r-x)
other = Read & Execute(r-x)
```

### Changing permission
The sommand to use when modifying permission is chmod. There are two ways to modify permissions, with numbers or with letters. Using letters. Using letters is easier to understand for most people. When modifying permissions be careful not to create security problems. Some files are configurate to have very restristive permissons to prevent unauthorized access.

```javascript
user@host:/home/user# ls -l /etc/shadow
-rw-r-----  1 root shadow 869 2005-11-08 13:16 /etc/shadow
user@host:/home/user#

Permissions:
owner = Read & Write (rw-)
group = Read (r--)
other = None (---)

Ownership:
owner = root
group = shadow
```

#### chmod with Letters

```javascript
Usage: chmod {options} filename
```
| user | ls output  |
| :----: | :--- |
|  u   |  owner |
|  g   |  group |
|  o   |  other |
|  a   |  all(same as ugo) |
|  x   |  execute |
|  w   |  write |
|  r   |  read |
|  +   |  add permission |
|  _   |  remove permission |
|  =   |  set permission |


Here are a few examples of chmod usage with letters.

##### First creatre some empty files
```javascript
local@local-OptiPlex-7020:~$ touch file1 file2 file3 file4
local@local-OptiPlex-7020:~$ ls -l
-rw-rw-r--  1 local local      0 Aug 23 11:45 file1
-rw-rw-r--  1 local local      0 Aug 23 11:45 file2
-rw-rw-r--  1 local local      0 Aug 23 11:45 file3
-rw-rw-r--  1 local local      0 Aug 23 11:45 file4
```

##### Add owner execute bit
```javascript
local@local-OptiPlex-7020:~$ chmod u+x file1
local@local-OptiPlex-7020:~$ ls -l
-rwxrw-r--  1 local local    0 Aug 23 11:47 file1
```

##### Add other write & execute bit
```javascript
local@local-OptiPlex-7020:~$ chmod o+wx file2
local@local-OptiPlex-7020:~$ ls -l
-rw-rw-rwx  1 local local    0 Aug 23 11:47 file2
```

##### Remove group read bit
```javascript
local@local-OptiPlex-7020:~$ chmod g-r file3
local@local-OptiPlex-7020:~$ ls -l
-rw--w-r--  1 local local    0 Aug 23 11:47 file3
```

##### Add read, write and execute to everyone
```javascript
local@local-OptiPlex-7020:~$ chmod ugo+rwx file4
local@local-OptiPlex-7020:~$ ls -l
-rwxrwxrwx  1 local local    0 Aug 23 11:47 file4
```

#### chmod with number

```javascript
Usage: chmod {options} filename
```

| Options | Definition  |
| :----: | :--- |
|  #--   |  owner |
|  -#-   |  group |
|  --#   |  other |
|  1   |  execute |
|  2   |  write |
|  4   |  read |

> Owner, Group and Other is represented by three numbers. To get the value for the options determine the type of access needed for the file then add.

For example if you want a file that has -rw-rw-rwx permissions you will use the following:

| Owner | Group  | Other |
| :----: | :---: |   :---:     |
|  read & write      |  read & write   |  read & write & execute   |
|  4+2=6     |  4+2=6   |   4+2+1=7    |

```javascript
user@host:/home/local$ chmod 667 filename
```
Another example if you want a file that has --w-r-x--x permissions you will use the following:
| Owner | Group  | Other |
| :----: | :---: |   :---:     |
|  write      |  read & execute   |   execute   |
|  2     |  4+1=5   |  1    |

```javascript
user@host:/home/local$ chmod 251 filename
```
Here are a few examples of chmod usage with numbers.

##### First create some empty files
```javascript
local@local-OptiPlex-7020:~$ touch file1 file2 file3 file4
local@local-OptiPlex-7020:~$ ls -l
-rw-rw-r--  1 local local    0 Aug 23 12:35 file1
-rw-rw-r--  1 local local    0 Aug 23 12:35 file2
-rw-rw-r--  1 local local    0 Aug 23 12:35 file3
-rw-rw-r--  1 local local    0 Aug 23 12:35 file4
```

##### Add onwer execute bit
```javascript
local@local-OptiPlex-7020:~$ chmod 744 file1
local@local-OptiPlex-7020:~$ ls -l
-rwxr--r--  1 local local    0 Aug 23 12:35 file1
```

##### Add other write & execute bit
```javascript
local@local-OptiPlex-7020:~$ chmod 647 file2
local@local-OptiPlex-7020:~$ ls -l
-rw-r--rwx  1 local local    0 Aug 23 12:35 file2
```

##### Remove group read bit
```javascript
local@local-OptiPlex-7020:~$ chmod 604 file3
local@local-OptiPlex-7020:~$ ls -l
-rw----r--  1 local local    0 Aug 23 12:35 file3
```

##### Add read & write & execute to everyone
```javascript
local@local-OptiPlex-7020:~$ chmod 777 file4
local@local-OptiPlex-7020:~$ ls -l
-rwxrwxrwx  1 local local    0 Aug 23 12:35 file4
```

#### chmod with sudo
Changing permissions on files that do not have ownership that changeing permission the wrong way on the wrong files can quickly mess up your system a great deal! Please be careful when using the `sudo`.

### Recursive Permission Changes
To change the permissions of multiple files and directories with one command. Please note the warning in the chmod with sudo section and the Warning with Recursive chmod section.

#### Recursive chmod with -R and sudo
To change all the permissions of each file and folder under a specified directory at once, use sudo chmod with -R

```javascript
user@host:/home/local$ sudo chmod 777 -R /path/to/someDirectory
user@host:/home/local$ ls -l
total 3
-rwxrwxrwx  1 user user 0 Nov 19 20:13 file1
drwxrwxrwx  2 user user 4096 Nov 19 20:13 folder
-rwxrwxrwx  1 user user 0 Nov 19 20:13 file2
```

#### Recursive chmod using find, pipemill, and sudo

To assign reasonably secure permissions to files and folders/directories, it's common to give files a permission of 644, and directories a 755 permission, since chmod -R assigns to both. Use sudo, the find command, and a pipemill to chmod as in the following examples.

To change permission of only files under a specified directory.
```javascript
user@host:/home/local$ sudo find /path/to/someDirectory -type f -print0 | xargs -0 sudo chmod 644
user@host:/home/local$ ls -l
total 3
-rw-r--r--  1 user user 0 Nov 19 20:13 file1
drwxrwxrwx  2 user user 4096 Nov 19 20:13 folder
-rw-r--r--  1 user user 0 Nov 19 20:13 file2
```

To change permission of only directories under a specified directory (including that directory):
```javascript
user@host:/home/local$ sudo find /path/to/someDirectory -type d -print0 | xargs -0 sudo chmod 755 
user@host:/home/local$ ls -l
total 3
-rw-r--r--  1 user user 0 Nov 19 20:13 file1
drwxr-xr-x  2 user user 4096 Nov 19 20:13 folder
-rw-r--r--  1 user user 0 Nov 19 20:13 file2
```

### Warning with Recursive chmod
WARNING: Although it's been said, it's worth mentioning in context of a gotcha typo. Please note, Recursively deleting or chown-ing files are extremely dangerous. You will not be the first, nor the last, person to add one too many spaces into the command. This example will hose your system:

```javascript
user@host:/home/local$ sudo chmod -R / home/john/Desktop/tempfiles
```


### Changing the File Owner and Group
A file's owner can be changed using the chown command. For example, to change the foobar file's owner to tux: 

```javascript
user@host:/home/local$ sudo chown tux foobar
```

o change the foobar file's group to penguins, you could use either chgrp or chown with special syntax:
```javascript
user@host:/home/local$ sudo chgrp penguins foobar

user@host:/home/local$ sudo chown :penguins foobar
```


Finally, to change the foobar file's owner to tux and the group to penguins with a single command, the syntax would be:
```javascript
user@host:/home/local$ sudo chown tux:penguins foobar
```