---
title: "Install OWAS ASST"
date: 2023-06-13T05:37:53+09:00
draft: false
tags: [OWASP,ASST,static-analysis,source-scan]
---

# ref
- https://owasp.org/ASST/

# install XAMPP -> FAIL
- https://sourceforge.net/projects/xampp/
  -> cannot install on LinuxVM on Apple silicon M2 (since XAMPP for Linux is only available for x64)

# Install Necessaries

## Apache2
```
$ sudo apt install apache2 mariadb-server
```

## MariaDB
```
$ sudo systemctl enable --now mariadb
$ systemctl status mariadb
```

## PHP
```
$ sudo apt install php
```

## Perl
```
$ sudo apt install perl
```

## Node.js
```
$ sudo apt install -y nodejs npm
```
Set a specific version of Node.js to let ASST work
```
$ sudo npm install n -g
$ sudo n 12.13.0
```



# Install ASST

### Clone the repository
```
$ git clone https://github.com/OWASP/ASST.git
```

### Config ASST

set ASST 
```
$ sudo mkdir -p /opt/lamp/htdocs
$ sudo mv ASST /opt/lamp/htdocs
$ cd /opt/lamp/htdocs/ASST
$ vi config.js
...
DEFAULT_PROJECT_PATH_TO_SCAN: "../ProjectFolderToScan/",
...
$ which php
/usr/bin/php
$ vi config_php_lang
...
IS_DBMS_USED: false, // it was TRUE before.
...
PHP_EXE_BIN_PATH: "/usr/bin/php", // Currently set for Windows // leave it empty "" if not set (if you don't have PHP binary) 
//PHP_EXE_BIN_PATH: "C:\\xampp\\php\\php.exe", // Currently set for Windows // leave it empty "" if not set (if you don't have PHP binary) 
...
```

### Create the ProjectFolder

```
$ sudo mkdir -p /opt/lamp/htdocs/ProjectFolderToScan
```

put source codes into the directory.

### run ASST
default command:
```
$ pwd
/opt/lamp/htdocs/ASST

$ sudo node /opt/lamp/htdocs/ASST/main.js
```

### RESULT

with a sample, I got:
```
└─$ sudo node /opt/lamp/htdocs/ASST/main.js

------------------------------
Scanning Web Site/App Security
------------------------------

<-- Checking for Injection Vulnerabilities -->
Number of Injections Found in the project is: 0
Well done!, No vulnerabilities found about Injection in your code, however there are some notices that you need to check them in the report.

-----------------------------------------------------------

<-- Checking for Broken Authentication Vulnerabilities -->
Number of Broken Authentications Found in the project is: 28

-----------------------------------------------------------

<-- Checking for Sensitive Data Exposure Vulnerabilities -->
Number of Sensitive Data Exposures Found in the project is: 401

-----------------------------------------------------------

<-- Checking for XML External Entity Injection Vulnerabilities -->
Number of XML External Entity Injections Found in the project is: 0
Well done!, No vulnerabilities found about XML External Entity Injection in your code, however there are some notices that you need to check them in the report.

-----------------------------------------------------------

<-- Checking for Security Misconfiguration Vulnerabilities -->
Number of Security Misconfigurations Found in the project is: 2

-----------------------------------------------------------

<-- Checking for Cross-Site Scripting Vulnerabilities -->
Number of Cross-Site Scriptings Found in the project is: 502

-----------------------------------------------------------

<-- Checking for Using Components With Known Vulnerabilities -->
Number of Using Old Componentss Found in the project is: 0
Well done!, No vulnerabilities found about Using Old Components in your code, however there are some notices that you need to check them in the report.                                                                                                 

-----------------------------------------------------------

<-- Checking for Cross-Site Request Forgery Vulnerabilities -->
Number of Broken Authentications Found in the project is: 27

-----------------------------------------------------------

<-- Checking for Server-Side Request Forgery Vulnerabilities -->
Number of Server-Side Request Forgery Problems Found in the project is: 0
Well done!, No vulnerabilities found about Server-Side Request Forgery Problem in your code, however there are some notices that you need to check them in the report.                                                                                  

-----------------------------------------------------------

<-- Checking for Extra Web Security Hardenings -->
Number of Extra Web Security Hardenings Found in the project is: 18

-----------------------------------------------------------

Total number of possible vulnerabilities found: 978
Check the generated report.html file to see scan results in detailed

-----------------------------------------------------------

Total scan time: 32 Seconds
```

and a report, `report.html` has been generated.







