---
title: "Sudo Attacks"
description: 
date: 2025-01-23T13:48:02Z
image: /post/sudo.jpg
math: 
license: 
hidden: false
comments: true
draft: false
tags: ["Privesc","kernel","Linux","Sudo"]
categories: ["Linux-Privilege-Escalation","Sudo-Attacks"] 
---

# Introduction to Sudo Attacks and Privilege Escalation on Linux

In the world of Linux-based operating systems, the `sudo` command plays a critical role in user privilege management. It allows permitted users to execute commands as the superuser (root) or another user, as specified by the security policy. However, various vulnerabilities have been discovered in `sudo` that can lead to privilege escalation, allowing unauthorized users to gain elevated access to system resources.

This discussion centers around several notable vulnerabilities, including CVE-2019-14287 and CVE-2019-18634, which have garnered attention for their potential to exploit the `sudo` command for malicious purposes. These vulnerabilities demonstrate the importance of understanding how `sudo` can be misconfigured or manipulated, leading to security breaches.

## Topics to Discuss

1. **CVE-2019-14287**:  CVE-2019-14287 is a vulnerability in `sudo` that allows a user with restricted privileges to execute arbitrary commands as **root**, even if they are explicitly denied in the `sudoers` file. This occurs due to improper validation of the user ID (`UID`) when running commands.

2. **CVE-2019-18634**: CVE-2019-18634 is a **buffer overflow vulnerability** in `sudo` that can allow local privilege escalation. It specifically affects `sudo` configurations where **pwfeedback** is enabled, which can result in memory corruption and potentially lead to unauthorized code execution or system crashes.
    
3. **Shell Escaping**: This topic focuses on the concept of shell escaping in command execution, particularly when arguments passed to the shell are not properly sanitized. Attackers can craft malicious input to manipulate shell behavior, leading to unintentional command executions with elevated privileges.
    
4. **LD_PRELOAD**: This is an environment variable that can be exploited to execute malicious shared libraries before any other libraries when running a program. By using `LD_PRELOAD`, attackers can intercept and modify calls to system functions within programs invoked with `sudo`, paving the way for privilege escalation.