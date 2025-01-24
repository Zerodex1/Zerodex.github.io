---
title: "Shell Escaping"
description: 
date: 2025-01-24T12:38:00Z
image: 
math: 
license: 
hidden: false
comments: true
draft: false
tags: ["Privesc","Linux","Sudo"]
categories: ["Linux-Privilege-Escalation","Sudo-Attacks"] 
---

# What's Sudo Shell Escaping

**Sudo shell escaping** refers to a technique where a user with limited privileges uses `sudo` to bypass restrictions and execute commands as a higher-privileged user, typically root. This can occur if the `sudo` configuration (the `sudoers` file) allows running commands that invoke a shell or provide shell-like functionality.

# Getting Privilege Escalation

To gain higher privileges using `sudo`, follow these steps:

1. Check how many commands you can run with `sudo` as a low-privilege user:

    ```shell
    sudo -l
    ```

  ![image alt text](post/sudo-l.png)

    As seen in the image, there are some tools and services that can be run as `sudo`. To make exploitation easier, we can visit this site: [GTFOBins](https://gtfobins.github.io/).

    This site provides a lot of useful information. Let's search for one of these services, such as **Vim**.

  ![image alt text](post/gtfbon.png)

    Simply copy the following command and paste it into your target terminal, and you will gain root privileges.

# Conclusion

In conclusion, sudo shell escaping is a powerful technique that can be exploited to gain unauthorized root access on a system. By identifying commands that can be run with elevated privileges, attackers can leverage tools like GTFOBins to execute malicious payloads and bypass security restrictions. Understanding these offensive methods is crucial for penetration testers and security professionals as they simulate attacks and identify vulnerabilities.