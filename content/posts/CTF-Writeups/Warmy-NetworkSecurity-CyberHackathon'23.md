---
author: "0xt0pus"
title: "Qualifier Network Security (Warmy) - CyberHackathon'23"
date: "2023-09-07"
description: ""
tags: ["CyberHackathon'23", "NetworkSecurity", "CTF-Walkthrough"]
ShowToc: true
TocOpen: false
---



A pcap file was being given for analysis.

The pcap file was being opened in the wireshark.

![](/writeups/warmy-CTF/1.png)


I applied the `http` filter to filter only http requests.

There was a zip file that was retrieved through http protocol. There was nothing interesting in this zip file.

![](/writeups/warmy-CTF/2.png)

I moved on, and i found that many requests were being made to the `/f_data/data` endpoint. All the requests were giving `403 forbidden` response except the last one, which gave `200 OK` response.

![](/writeups/warmy-CTF/3.png)

I sent it HTTP Stream as shown below. In response there was data, which i didn't understand initially.

![](/writeups/warmy-CTF/4.png)

When i dig further then i got another message, which was giving us a hint that the previous data possibly encrypted with `RC4` algorithm. But for that we need a key, and we do not have key yet.

![](/writeups/warmy-CTF/5.png)

When i further analyzed the same `http` data, there was another message, which gave us the key. It was sending request to `/f.php` with different values for the get parameter `t`. When i combined the parameters values, it was `secret` which might be the key.

![](/writeups/warmy-CTF/6.png)

So now its time to put everything together and try it. I opened [cyberchef](https://cyberchef.org/) and chose the RC4 algorithm. I put all the found info as shown below.

![](/writeups/warmy-CTF/7.png)

I didn't work :). The data we copied from the Wireshark was in ASCII format, which was the mistake. ASCII has limitations in representing characters beyond the basic Latin alphabet. So it is possible that data includes characters from different character sets (e.g., UTF-8). So, i chose RAW format from the Wireshark as shown below.

![](/writeups/warmy-CTF/8.png)

Now, i copied the data in last line, as which is the data in response.

This time, i used the RAW format data in cyberchef, which worked as shown.

![](/writeups/warmy-CTF/9.png)

Hope you liked it.