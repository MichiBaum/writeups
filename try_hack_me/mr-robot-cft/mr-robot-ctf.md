# Mr Robot Ctf

---
Date: 2021-12-21  
Author: Michael Baumberger  
[TryHackMe link](https://tryhackme.com/room/mrrobot)
---

# Task 2 | Hack the machine

First we scan the server with nmap, nikto and dirbuster:

````
nmap -sC -sV -p- 10.10.48.240
Starting Nmap 7.92 ( https://nmap.org ) at 2021-12-21 19:12 CET
Nmap scan report for 10.10.48.240
Host is up (0.071s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache
443/tcp open   ssl/http Apache httpd
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=www.example.com
| Not valid before: 2015-09-16T10:45:03
|_Not valid after:  2025-09-13T10:45:03
|_http-server-header: Apache

Nmap done: 1 IP address (1 host up) scanned in 338.05 seconds
````

And we run nikto:

````
nikto -h 10.10.48.240
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.48.240
+ Target Hostname:    10.10.48.240
+ Target Port:        80
+ Start Time:         2021-12-21 19:12:40 (GMT1)
---------------------------------------------------------------------------
+ Server: Apache
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Retrieved x-powered-by header: PHP/5.5.29
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Uncommon header 'tcn' found, with contents: list
+ Apache mod_negotiation is enabled with MultiViews, which allows attackers to easily brute force file names. See http://www.wisec.it/sectou.php?id=4698ebdc59d15. The following alternatives for 'index' were found: index.html, index.php
+ OSVDB-3092: /admin/: This might be interesting...
+ OSVDB-3092: /readme: This might be interesting...
+ Uncommon header 'link' found, with contents: <http://10.10.48.240/?p=23>; rel=shortlink
+ /wp-links-opml.php: This WordPress script reveals the installed version.
+ OSVDB-3092: /license.txt: License file found may identify site software.
+ /admin/index.html: Admin login page/section found.
+ Cookie wordpress_test_cookie created without the httponly flag
+ /wp-login/: Admin login page/section found.
+ /wordpress: A Wordpress installation was found.
+ /wp-admin/wp-login.php: Wordpress login found
+ /wordpresswp-admin/wp-login.php: Wordpress login found
+ /blog/wp-login.php: Wordpress login found
+ /wp-login.php: Wordpress login found
+ /wordpresswp-login.php: Wordpress login found
+ 7890 requests: 0 error(s) and 19 item(s) reported on remote host
+ End Time:           2021-12-21 20:09:14 (GMT1) (3394 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
````

And dirbuster:

````
dirb http://10.10.48.240

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Tue Dec 21 19:12:19 2021
URL_BASE: http://10.10.48.240/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://10.10.48.240/ ----
==> DIRECTORY: http://10.10.48.240/0/
==> DIRECTORY: http://10.10.48.240/admin/
+ http://10.10.48.240/atom (CODE:301|SIZE:0)
==> DIRECTORY: http://10.10.48.240/audio/
==> DIRECTORY: http://10.10.48.240/blog/
==> DIRECTORY: http://10.10.48.240/css/
+ http://10.10.48.240/dashboard (CODE:302|SIZE:0)
+ http://10.10.48.240/favicon.ico (CODE:200|SIZE:0)
==> DIRECTORY: http://10.10.48.240/feed/
==> DIRECTORY: http://10.10.48.240/image/
==> DIRECTORY: http://10.10.48.240/Image/
==> DIRECTORY: http://10.10.48.240/images/
+ http://10.10.48.240/index.html (CODE:200|SIZE:1188)
+ http://10.10.48.240/index.php (CODE:301|SIZE:0)
+ http://10.10.48.240/intro (CODE:200|SIZE:516314)
==> DIRECTORY: http://10.10.48.240/js/
+ http://10.10.48.240/license (CODE:200|SIZE:309)
+ http://10.10.48.240/login (CODE:302|SIZE:0)
+ http://10.10.48.240/page1 (CODE:301|SIZE:0)
+ http://10.10.48.240/phpmyadmin (CODE:403|SIZE:94)
+ http://10.10.48.240/rdf (CODE:301|SIZE:0)
+ http://10.10.48.240/readme (CODE:200|SIZE:64)
+ http://10.10.48.240/robots (CODE:200|SIZE:41)
+ http://10.10.48.240/robots.txt (CODE:200|SIZE:41)
+ http://10.10.48.240/rss (CODE:301|SIZE:0)
+ http://10.10.48.240/rss2 (CODE:301|SIZE:0)
+ http://10.10.48.240/sitemap (CODE:200|SIZE:0)
+ http://10.10.48.240/sitemap.xml (CODE:200|SIZE:0)
==> DIRECTORY: http://10.10.48.240/video/
==> DIRECTORY: http://10.10.48.240/wp-admin/
+ http://10.10.48.240/wp-config (CODE:200|SIZE:0)
==> DIRECTORY: http://10.10.48.240/wp-content/
+ http://10.10.48.240/wp-cron (CODE:200|SIZE:0)
==> DIRECTORY: http://10.10.48.240/wp-includes/
+ http://10.10.48.240/wp-links-opml (CODE:200|SIZE:227)
+ http://10.10.48.240/wp-load (CODE:200|SIZE:0)
+ http://10.10.48.240/wp-login (CODE:200|SIZE:2606)
+ http://10.10.48.240/wp-mail (CODE:500|SIZE:3064)
+ http://10.10.48.240/wp-settings (CODE:500|SIZE:0)
+ http://10.10.48.240/wp-signup (CODE:302|SIZE:0)
+ http://10.10.48.240/xmlrpc (CODE:405|SIZE:42)
+ http://10.10.48.240/xmlrpc.php (CODE:405|SIZE:42)

---- Entering directory: http://10.10.48.240/0/ ----
^C
````

From the nmap scan we get that port 22 ssh and apache listening to port 80 http and 443 https.

The dirbuster scan gives us many results but for the start we should keep in mind this pages:

- http://10.10.48.240/admin
- http://10.10.48.240/robots
- http://10.10.48.240/wp-admin
- http://10.10.48.240/phpmyadmin

Many other pages look like they are generated from wordpress.

We first look at the http://10.10.48.240/robots and see an entry called 'key-1-of-3.txt'.
If we try to get this file with **http://10.10.48.240/key-1-of-3.txt we get our first flag**.

From the nikto scan we get the interesting urls '/admin' and '/readme' witch we should look at.
And we also get the information that in the file '/wp-links-opml.php' the *wordpress version 4.3.1* is leeked:

````xml
<opml version="1.0">
    <head>
        <title>Links for user's Blog!</title>
        <dateCreated>Tue, 21 Dec 2021 19:00:50 GMT</dateCreated>
        <!--  generator="WordPress/4.3.1"  -->
    </head>
    <body> </body>
</opml>
````

