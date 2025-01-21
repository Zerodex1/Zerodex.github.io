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
tags: ["Privesc","Enumeration","Linux"]
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

## Network Enumeration

**Network enumeration involves discovering network configurations, active connections, and accessible services to identify potential attack vectors for privilege escalation or lateral movement.**

Show Network Interfaces:
```shell
ip a
ifconfig
```

Routing Table:
```shell
ip route
route -n
```

View Active Network Connections:
```shell
netstat -tuln
ss -tuln
```

DNS Configuration:
```shell
cat /etc/resolv.conf
```

ARP Table:
```shell
arp -a
ip neigh
```

Ping to Test Connectivity:
```shell
ping -c 4 <target-ip>
```

Traceroute to Map Network Path:
```shell
traceroute <target-ip>
```

Identify Listening Ports:
```shell
netstat -tulnp
lsof -i
```

Scan Open Ports on Target:
```shell
nmap -sS -p- <target-ip>
```

## Password Enumeration

**Password hunting involves searching for stored credentials, configuration files, or weakly protected secrets that can be used to gain unauthorized access or escalate privileges.**

1. Some applications and services store sensitive information, including credentials, in configuration files.

Common Locations:
```shell
find / -name "*.conf" 2>/dev/null
find / -name "*.yml" 2>/dev/null
find / -name "*.ini" 2>/dev/null
```
Look in directories like /etc/, /var/, /opt/, and /home/.
Examples of Files to Check:
```shell
/etc/passwd
/etc/shadow (requires root or elevated privileges)
/etc/ssh/sshd_config
```

2. Check History Files
Users often type sensitive commands that get stored in shell history files.
```shell
cat ~/.bash_history
cat ~/.zsh_history
```
Look for commands like ssh, sudo, mysql, or any with plaintext passwords.

3. Search for Files Containing the Word "Password"
Look for plaintext credentials in files.
```shell
grep -ri "password" / 2>/dev/null
```
Include variations like "passwd," "pass," or "pwd."

4. Search for SSH Keys
SSH private keys can be used to log into other systems.
```shell
find / -name "id_rsa" 2>/dev/null
```
Check default paths like ~/.ssh/.

5. Look for Database Credentials
Applications often store database credentials in config files.
```shell
grep -ri "DB_USER" / 2>/dev/null
grep -ri "DB_PASS" / 2>/dev/null
```
Files to check:
wp-config.php (WordPress)
.env (Laravel or modern apps)
settings.py (Django)

6. Dump Memory for Passwords
Sometimes passwords can be found in running memory.
```shell
strings /dev/mem | grep -i "password"
```
Alternatively, check running processes:
```shell
 ps aux |grep -i "password"
```

7. Check for Backup Files
Old backups may contain sensitive data.
```shell
find / -name "*.bak" 2>/dev/null
find / -name "*.old" 2>/dev/null
find / -name "*.backup" 2>/dev/null
```

8. Identify Open Connections for Exposed Credentials
Applications exposing credentials via environment variables or arguments can be a goldmine.
```shell
env | grep -i "pass"
```
Check processes:
```shell
ps aux | grep -E "password|username|key"
```

9. Look for Git or Repository Secrets
Repositories often accidentally include credentials.
```shell
find / -name ".git" 2>/dev/null
grep -r "password" .git 2>/dev/null
```

10. Dump Browser Credentials
Browsers often save login information.

Firefox:
```shell
cat ~/.mozilla/firefox/*.default-release/logins.json
```

Chrome: Credentials are stored in `~/.config/google-chrome/Default/Login Data (encrypted).

11. Enumerate Credentials in Docker or Kubernetes

Docker:
```shell
docker inspect <container_id>
```

Kubernetes:
```shell
kubectl get secrets
```

12. Extract Passwords from Files Using Tools
Tools like strings, binwalk, or exiftool can help extract data from binary files.
```shell
strings <file> | grep -i "password"
```

13. Enumerate Web Credentials
Search web server files for hardcoded credentials:
```shell
grep -ri "auth" /var/www/html/
```

14. Advanced: Check for Password Reuse
If you find a password or hash, try it across other accounts/services.
```shell
su -c "<found_password>" <username>
```

## Automation tools 

**Automation tools help streamline the process of enumeration, vulnerability discovery, and privilege escalation by automating repetitive tasks and identifying potential weaknesses efficiently. In my opinion, mastering the process manually is preferable.**

1. **LinPEAS**

**LinPEAS** (Linux Privilege Escalation Awesome Script) is a powerful script designed to enumerate a Linux system for privilege escalation vectors and misconfigurations.
 • Download Source: LinPEAS GitHub Repository [LinPeas](https://github.com/peass-ng/PEASS-ng)
 
 1. **Linux Exploit Suggester**
This tool identifies potential privilege escalation vulnerabilities based on the system’s kernel version and configuration.
 • Download Source: **Linux Exploit Suggester** GitHub Repository [Linux Exploit Suggester](https://github.com/The-Z-Labs/linux-exploit-suggester)
 
 3. **LinEnum**
 
**LinEnum** is a lightweight script that performs comprehensive enumeration of a Linux system, including user details, file permissions, and potential privilege escalation paths.
 • Download Source: LinEnum GitHub Repository  [LinEnum](https://github.com/rebootuser/LinEnum)

 4. **Pspy**
 
**Pspy** is a process monitoring tool that allows you to watch processes executed on a Linux system without needing elevated privileges.
 • Download Source: pspy GitHub Repository [Pspy](https://github.com/DominicBreuker/pspy)


 **Conclusion**
In this article, I’ve shared key aspects of enumeration to help us master privilege escalation, from system and user insights to files, networks, and passwords. While tools can assist, I believe manual expertise is crucial. Thanks for reading, and happy learning!