# Fail2Ban

[Github](https://github.com/fail2ban/fail2ban)
This page is mostly written with fail2ban version 0.11.2.

## Configuration
You can configure Fail2Ban using the files in ``/etc/fail2ban``. It is possible to configure the server using commands sent to it by ``fail2ban-client``.
Your custom configuration should be under ``/etc/fail2ban/jail.d``


### Basic sshd jail
```Bash
[sshd]
enabled  = true
# "bantime.increment" allows to use database for searching of previously banned ip's to increase a default ban time using special formula, default it is banTime * 1, 2, 4, 8, 16, 32...
bantime.increment = true
# "bantime.overalljails" (if true) specifies the search of IP in the database will be executed cross over all jails, if false (dafault), only current jail of the ban IP will be searched
bantime.overalljails = true
# A host is banned if it has generated "maxretry" during the last "findtime" seconds.
findtime  = 1d
# "maxretry" is the number of failures before a host get banned.
maxretry = 5
bantime  = 1w
```


## Helpfull commands
See all jails that are active:

```Bash
fail2ban-client status
Status
|- Number of jail:      1
`- Jail list:   sshd
```


You wanna know what the stats are of a specific jail (in this example sshd)?

```Bash
/etc/fail2ban/jail.d# fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 121
|  |- Total failed:     11558
|  `- File list:        /var/log/auth.log
`- Actions
   |- Currently banned: 730
   |- Total banned:     3318
   `- Banned IP list:   .....
```


If you changed configs you can reload them. 
```Bash
fail2ban-client reload --all
```


You can get all banned ip addresses in a dictionary with:

```Bash
fail2ban-client banned
```