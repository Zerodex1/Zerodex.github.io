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

Table of contents :


	- System and User Enumeration
	- Files Enumeration
	- Network Enumeration
	- Password Enumeration
	- Automation Tools 


## System and User Enumeration

**System and user enumeration involves gathering information about the operating system, users, groups, and privileges to identify potential weaknesses for privilege escalation.**


- `uname`: The `uname` command, when used with the `-a` option, displays detailed information about the Linux kernel:

```bash
 uname -a
```

- `cat /etc/os-release`: This command displays information about the target system’s distribution and version:

```bash
 cat /etc/os-release
```

- `cat /proc/version`: This command displays more detailed information about the Linux kernel, including the compiler used to build it:

```bash
 cat /proc/version
```

Current User and Group:

```bash
 whoami
 id
 groups
```

Logged-in Users:

```shell
 w
 last
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

## Files Enumeration

**File enumeration involves searching for sensitive files, misconfigurations, or improper permissions that can be exploited to escalate privileges or gain unauthorized access.**

Search for World-Writable Files:
```shell
find / -type f -perm -o+w 2>/dev/null
```

Search for World-Writable Directories:
```shell
find / -type d -perm -o+w 2>/dev/null
```

Search for Files Owned by Root:
```shell
find / -uid 0 -type f 2>/dev/null
```

Search for Files with SUID Bit:
```shell
find / -perm /4000 -type f 2>/dev/null
```

Search for Files with SGID Bit:
```shell
find / -perm /2000 -type f 2>/dev/null
```

Search for Configuration Files Containing Credentials:
```shell
grep -Ri "password" /etc/* 2>/dev/null
grep -Ri "passwd" /home/* 2>/dev/null
```

Identify Writable Files in System Directories:
```shell
find /etc -writable 2>/dev/null
```

Look for Backup Files:
```shell
find / -name "*.bak" -o -name "*.backup" 2>/dev/null
```

Search for SSH Keys:
```shell
find / -name "id_rsa" -o -name "id_dsa" 2>/dev/null
```

Search for Sensitive Application Configuration Files;

**Apache:**
```shell
cat /etc/httpd/conf/httpd.conf
```

**MySQL:**
```shell
cat /etc/mysql/my.cnf
```

Find Recent Files Modified or Created:
```shell
find / -type f -mtime -5 2>/dev/null
```

Find Hidden Files:
```shell
find / -name ".*" 2>/dev/null
```

Identify Files Owned by Current User:
```shell
find / -user $(whoami) 2>/dev/null
```

Check for Backup/Temp Files in Home Directories:
```shell
ls -laR /home/ | grep "~"
```

