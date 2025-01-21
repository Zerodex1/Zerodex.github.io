---
title: "Linux Enumeration"
description: 
date: 2025-01-21T14:22:34Z
image: 
math: 
license: 
hidden: false
comments: true
draft: false
tags: ["Privesc","Enumeration"]
categories: [Linux-Privilege-Escalation]
---

# linux Enumeration For Privilege esclation

In this article I will share with you some techniques for enumerate linux systenm 

tables of content :


	- System and User Enumeration
	- Files Enumeration
	- Network Enumeration
	- Password Enumeration
	- Automation Tools 


## System and User Enumeration

System and user enumeration involves gathering information about the operating system, users, groups, and privileges to identify potential weaknesses for privilege escalation.


- `uname`: The `uname` command, when used with the `-a` option, displays detailed information about the Linux kernel:

```bash
$ uname -a
```

- `cat /etc/os-release`: This command displays information about the target system’s distribution and version:

```bash
$ cat /etc/os-release
```

- `cat /proc/version`: This command displays more detailed information about the Linux kernel, including the compiler used to build it:

```bash
$ cat /proc/version
```

Current User and Group:

```bash
$ whoami
$ id
$ groups
```

Logged-in Users:

```shell
$ w
$ last
```

System Users:

```shell
cat /etc/passwd
awk -F: '$3 < 1000 {print $1}' /etc/passwd
```

Sudo Permissions:

```shell
sudo -l
```

Environment Variables:

```shell
env
```

Processes Owned by Other Users:

```shell
ps aux
```

Hidden Users (Check for unusual configurations):

```shell
ls -la /home/
```

Files with SUID/SGID Bit:

```shell
find / -perm /6000 -type f 2>/dev/null
```
