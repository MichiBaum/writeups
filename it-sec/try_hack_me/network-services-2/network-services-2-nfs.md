---
Author: Michael Baumberger
title: Network Services 2 Nfs
parent: Network Services 2
ancestor: Try Hack Me Writeups
---

# Network Services 2 Nfs

## Task 3 | Enumerating NFS

First we nmap scan the server:

    nmap -p- -A -sC -Pn 10.10.178.180
    Starting Nmap 7.92 ( https://nmap.org ) at 2021-12-19 10:57 CET
    Nmap scan report for 10.10.178.180
    Host is up (0.046s latency).
    Not shown: 65528 closed tcp ports (reset)
    PORT      STATE SERVICE  VERSION
    22/tcp    open  ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey:
    |   2048 73:92:8e:04:de:40:fb:9c:90:f9:cf:42:70:c8:45:a7 (RSA)
    |   256 6d:63:d6:b8:0a:67:fd:86:f1:22:30:2b:2d:27:1e:ff (ECDSA)
    |_  256 bd:08:97:79:63:0f:80:7c:7f:e8:50:dc:59:cf:39:5e (ED25519)
    111/tcp   open  rpcbind  2-4 (RPC #100000)
    2049/tcp  open  nfs      3-4 (RPC #100003)
    40681/tcp open  nlockmgr 1-4 (RPC #100021)
    47415/tcp open  mountd   1-3 (RPC #100005)
    50853/tcp open  mountd   1-3 (RPC #100005)
    57587/tcp open  mountd   1-3 (RPC #100005)
    
    Nmap done: 1 IP address (1 host up) scanned in 680.49 seconds

We can see that **7 ports are open** and the **nfs port is 2049**.

We now can show the nfs shares with:

    showmount -e 10.10.178.180
    Export list for 10.10.178.180:
    /home *

We now can mount the **nfs /home** from above. To do that we create a directory like /tmp/networkservices2 and mount the share:

    mount -t nfs 10.10.178.180:/home /tmp/networkservices2 -o nolock
    mount.nfs: access denied by server while mounting 10.10.178.180:/home

I got an error but normally (if you dont use f* wsl2) you shouldn't get an error. To resolve it run the above command with the -v argument to get the verbose output:

    mount -t nfs 10.10.178.180:/home /tmp/networkservices2 -o nolock -v
    mount.nfs: timeout set for Sun Dec 19 11:17:57 2021
    mount.nfs: trying text-based options 'nolock,vers=4.2,addr=10.10.178.180,clientaddr=172.26.102.250'
    mount.nfs: mount(2): Invalid argument
    mount.nfs: trying text-based options 'nolock,vers=4.1,addr=10.10.178.180,clientaddr=172.26.102.250'
    mount.nfs: mount(2): Invalid argument
    mount.nfs: trying text-based options 'nolock,vers=4.0,addr=10.10.178.180,clientaddr=172.26.102.250'
    mount.nfs: mount(2): Operation not permitted
    mount.nfs: trying text-based options 'nolock,addr=10.10.178.180'
    mount.nfs: prog 100003, trying vers=3, prot=6
    mount.nfs: trying 10.10.178.180 prog 100003 vers 3 prot TCP port 2049
    mount.nfs: prog 100005, trying vers=3, prot=17
    mount.nfs: trying 10.10.178.180 prog 100005 vers 3 prot UDP port 56278
    mount.nfs: mount(2): Permission denied
    mount.nfs: access denied by server while mounting 10.10.178.180:/home



## Task 4 | Exploiting NFS
