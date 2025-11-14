---
author: "0xt0pus"
title: "HackTheBox keeper machine Walkthrough"
date: "2024-08-18"
description: "Keeper is an easy level Hackthebox machine, which runs SSH and Web services. The web server uses default service credentials and provides admin level access on the web server. The SSH password of a user is leaked on the web server which can be used to obtain the user level access of the machine. The home directory of the user is serving a memory dump. This dump teaches the CVE-2023-32784. This was a vulnerability in the Keepass, where the master password of password vault Keepass is stored in the memory. The exploit of this CVE is used to obtained master password of the vault. This vault has the root user putty key file, which was converted to the SSH private key format and was being used to obtain root level access. "
tags: ["Hackthebox", "CVE", "Machine-Walkthrough"]
cover:
    image: "/writeups/Keeper-Hackthebox/Keeper.jpg"

ShowToc: true
TocOpen: false
---

## Enumeration

All the ports were scanned. 

```bash
┌──(kali㉿kali)-[~/Desktop/hackthebox/keeper]
└─$ nmap -p- --min-rate 1000 keeper.htb --oN AllPortScan.txt
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-18 18:13 EDT
Nmap scan report for keeper.htb (10.10.11.227)
Host is up (0.032s latency).
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

```

The ssh and http port were open. 

The service version and OS enumeration is being carried out with the following command.
```bash
┌──(kali㉿kali)-[~/Desktop/hackthebox/keeper]
└─$ nmap -p22,80 -A keeper.htb --oN ServiceVersion.txt                    
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-18 18:14 EDT
Stats: 0:00:07 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.30% done; ETC: 18:14 (0:00:00 remaining)
Nmap scan report for keeper.htb (10.10.11.227)
Host is up (0.030s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:39:d4:39:40:4b:1f:61:86:dd:7c:37:bb:4b:98:9e (ECDSA)
|_  256 1a:e9:72:be:8b:b1:05:d5:ef:fe:dd:80:d8:ef:c0:66 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Site doesnt have a title (text/html).
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
It shows the web server is using nginx and the underlying OS is Linux. 

The website is being browsed. 

![](/writeups/Keeper-Hackthebox/1.png)

Added the `tickets.keeper.htb` in the hosts file, in order to access the `tickets.keeper.htb`.

The `tickets.keeper.htb` has a login page as shown below: 
![](/writeups/Keeper-Hackthebox/2.png)


Searched about the default credentials of `Request Tracker` and found the following credentials:
![](/writeups/Keeper-Hackthebox/3.png)

These credentials worked and I logged into the site as shown.
![](/writeups/Keeper-Hackthebox/4.png)

Under  `Admin>Users`, selected the `lnorgaard`. It shows the ssh password of this user as shown:
![](/writeups/Keeper-Hackthebox/5.png)

## Gaining Access:

Used the found password to log into the ssh. 
![](/writeups/Keeper-Hackthebox/6.png)


There is a memory dump file in the home directory of the user `lnorgaard` as shown above. 

Transferred it in my own kali machine and extracted it. 
![](/writeups/Keeper-Hackthebox/7.png)


This was the dump of the `Keepass` application. The keepass application below 2.54 has the vulnerability, in which the master password is stored in the memory of the system. There is an official POC https://github.com/vdohney/keepass-password-dumper.git, which can be used to obtain the master password from the memory. As I was not having the dotnet environment installed, I found the alternative python based implementation [HERE](https://github.com/matro7sh/keepass-dump-masterkey) . So I used the python based implementation.  I used this one and retrieved the password as shown below. 
![](/writeups/Keeper-Hackthebox/8.png)

## Privilege Escalation

The above returned weird sort of password, so I googled it and considered the suggested word as password. 

![](/writeups/Keeper-Hackthebox/9.png)


Entered the password, which worked. There was a putty key file in the `keeper.htb`, I checked the content of this. 
![](/writeups/Keeper-Hackthebox/10.png)


Saved this putty key in the file and run the following command to convert this into the format of openssh private key. 
```bash
puttygen putty-pass-file -O private-openssh -o id_rsa

# Here 

# -O: the format in which we want to convert the putty key file
# -o: The file where we want to save the generated file. 

```

![](/writeups/Keeper-Hackthebox/11.png)

Used this SSH private key to log in to to the system as root used. 

![](/writeups/Keeper-Hackthebox/12.png)


Hence, machine it rooted!!
