---
author: "0xt0pus"
title: "Bypass square -  Web - NaScon'23"
date: "2024-02-02"
description: "Bypass two preg_match functions to get the flag."
tags: ["NaSCon'23", "Web", "CTF-Walkthrough"]
ShowToc: true
TocOpen: false
---




In this challenge, we have to bypass two preg_match functions to get the flag.

#### The Given Code

```php
<?php
#read flag.txt

highlight_file(__FILE__);

$code = $_GET['cmd']; 
$blocked_functions = '/(exec|shell_exec|system|`)/i'; 

if (preg_match($blocked_functions, $code)) {
    die("Hacking attempt detected");
} else {
  $blocked_functions = "/flag/i";  
  if (preg_match($blocked_functions, $code)) {
    die("Hacking attempt detected");
  } 
  else{
    eval(urldecode($code));
  }
}
?>
```


Initially, It highlights the code file. Then it stores the 'cmd' get parameter value in code variable, it blocks all the functions through which we can execute the shell commands. 

It also blocks the flag keyword. 

At the end, it URL decode the code variable and run eval function on that. 

In PHP, `eval()` is a function that takes a string as an argument and executes it as PHP code. 

So initially I tried executing `echo "hello world";`

It runs as shown below. 


![](/writeups/bypassSquare-CTF/1.png)


Our goal is to read the flag.txt file that exists in the same directory. I tried if we can somehow read the flag.txt directly, but it is forbidden as shown below. 

![](/writeups/bypassSquare-CTF/2.png)


Now I searched the way to read file in php, so I got to know that we can use `file_get_contents()` to read file in php. 

We can the following to read flag.txt: `cmd=file_get_contents('flag.txt');`

But the issue is that, the flag word is blocked. 

As we can see that, it decodes url before sending it to the eval function as show below. 

```php
eval(urldecode($code));
```

So, we can double URL encode it before sending it, so first time, our own URL will decode it and the second time the `urldecode()` function will decode it. So I used burp to encode it as shown below. 

![](/writeups/bypassSquare-CTF/3.png)

When we double encode it: `file_get_contents('flag.txt');`, it will be very longer. So instead I double encoded just the g word in flag.txt. 

So the final payload is: 

```php
cmd=echo file_get_contents('fla%25%36%37.txt');
```

So I run it, it gave me the flag.txt as show below. 

![](/writeups/bypassSquare-CTF/4.png)

<br><br>
I hope you liked it. :)😍






