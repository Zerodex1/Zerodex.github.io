---
title: "SUID Attacks"
description: 
date: 2025-01-26T12:47:23Z
image: /post/coversuid.jpg
math: 
license: 
hidden: false
comments: true
draft: false
tags: ["Privesc","Linux","SUID"]
categories: ["Linux-Privilege-Escalation","SUID-Attacks"] 
---

# What is SUID?

**Definition:** SUID (Set owner User ID up on execution) is a special permission that allows other users run with the owner’s privileges. That’s why SUID files can be exploited to give adversaries the higher privilege in Linux/Unix system called privilege escalation. Mostly, **root** access is the goal of hackers when performing privilege escalation.

## Techniques : 
- Shared Object Injection
- Binary Symlinks
- Environment Variables 

## How to find the SUID files

The following command will list all of the SUID files in the system

```shell
 find / -perm -u=s -type f 2>/dev/null
```


- `find`: a Linux command to search for files in a directory hierarchy
- `-perm`: is used to define the permissions to search for
- `-u=s`: search for files with the SUID permission
- `-type f`: search for regular file
- `2>dev/nul`l: errors will be deleted automatically