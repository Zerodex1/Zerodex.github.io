---
title: "Binary Symlinks"
description: 
date: 2025-01-30T17:41:45Z
image: post/binary.jpg
math: 
license: 
hidden: false
comments: true
draft: false
tags: ["Privesc","Linux","SUID"]
categories: ["Linux-Privilege-Escalation","SUID-Attacks"]
---

### **Binary Symlink Privilege Escalation**

Binary symlink privilege escalation is a technique where an attacker abuses symbolic links (symlinks) to trick a privileged program into executing malicious code or performing unintended actions with elevated privileges. This type of attack often targets misconfigured or insecurely coded scripts or binaries that execute with higher privileges, such as `root`.

---

### **How It Works**

1. **Understanding Symlinks**:
    
    - A symlink is a file that points to another file or directory.
    - For example:
        ```bash
        ln -s /path/to/target /path/to/symlink
        ```
        
2. **Privilege Escalation via Symlinks**:
    
    - An attacker creates a symlink pointing to a malicious binary or file.
    - A privileged program inadvertently interacts with the symlink, allowing the attacker to manipulate the file or execute code with elevated privileges.

---

### **Scenarios for Exploiting Symlinks**

1. **Writable Directories in Privileged Context**:
    
    - If a privileged program creates or interacts with files in a directory writable by unprivileged users, an attacker can replace those files with symlinks pointing to sensitive locations like `/etc/passwd`.
2. **Temporary File Vulnerabilities**:
    
    - Programs that create temporary files without securely checking their existence are susceptible to symlink attacks.
    - Example:
        - A script creates a temporary file `/tmp/somefile` without checking if it already exists.
        - The attacker creates a symlink `/tmp/somefile` pointing to `/etc/shadow`.
        - When the privileged program writes to `/tmp/somefile`, it overwrites `/etc/shadow`.
3. **Replacing Binaries**:
    
    - If a privileged service or cron job executes a script from a writable directory, an attacker can replace it with a symlink pointing to a malicious script.

---

### **Practical Example**

#### 1. Symlink Attack to Modify `/etc/passwd`

- Assume a root script copies a file to `/etc/passwd` without checking its destination:
    
    ```bash
    cp /tmp/userfile /etc/passwd
    ```
    
- Exploitation:
    
    ```bash
    ln -s /etc/passwd /tmp/userfile
    echo "malicious_entry:x:0:0:root:/root:/bin/bash" >> /tmp/userfile
    ```
    
- When the script runs, the symlink redirects `/tmp/userfile` to `/etc/passwd`, adding the malicious entry with root privileges.

#### 2. Temporary File Exploitation

- A root-owned script writes logs to `/tmp/log.txt`:
    
    ```bash
    echo "System Log" > /tmp/log.txt
    ```
    
- Exploitation:
    
    ```bash
    ln -s /etc/shadow /tmp/log.txt
    ```
    
- The script overwrites `/etc/shadow`, potentially corrupting it or injecting malicious content.

---

### **Mitigations**

1. **Secure Temporary File Creation**:
    
    - Use secure functions like `mktemp` to create unique temporary files.
        
        ```bash
        temp_file=$(mktemp)
        echo "Secure Data" > "$temp_file"
        ```
        
2. **Validate Symlinks**:
    
    - Use the `O_NOFOLLOW` flag when opening files to avoid following symlinks:
        
        ```c
        open("/tmp/file", O_WRONLY | O_CREAT | O_NOFOLLOW);
        ```
        
3. **Restrict Permissions**:
    
    - Ensure sensitive directories and files are not writable by unprivileged users.
    - For example, `/tmp` should have the `sticky` bit set:
        
        ```bash
        chmod +t /tmp
        ```
        
4. **Avoid Privileged Actions on User-Controlled Files**:
    
    - Avoid executing privileged operations on files in user-writable locations.
5. **Monitor and Audit**:
    
    - Regularly monitor for suspicious symlinks in writable directories using tools like `find`:
        
        ```bash
        find /tmp -type l -ls
        ```
        
---