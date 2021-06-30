# Reduce Attack Surface


## 01. Create an Attack Surface
---
Intall Openlitespeed and port 7080 will be taken up by the server process. 
https://openlitespeed.org/kb/install-ols-from-litespeed-repositories/

```
ubuntu@ip-172-31-22-219:~$ sudo wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh | sudo bash
--2021-06-29 18:19:24--  http://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh
Resolving rpms.litespeedtech.com (rpms.litespeedtech.com)... 52.55.120.73
Connecting to rpms.litespeedtech.com (rpms.litespeedtech.com)|52.55.120.73|:80... connected.

N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension
 All done, congratulations and enjoy !

ubuntu@ip-172-31-22-219:~$ sudo apt-get install openlitespeed

ubuntu@ip-172-31-22-219:~$ sudo systemctl status openlitespeed
● lshttpd.service - OpenLiteSpeed HTTP Server
   Loaded: loaded (/etc/systemd/system/lshttpd.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2021-06-29 18:20:24 UTC; 1min 2s ago
  Process: 16287 ExecStart=/usr/local/lsws/bin/lswsctrl start (code=exited, status=0/SUCCESS)
 Main PID: 16330 (litespeed)
   CGroup: /system.slice/lshttpd.service
           ├─16330 openlitespeed (lshttpd - main)
           ├─16340 openlitespeed (lscgid)
           ├─16371 openlitespeed (lshttpd - #01)
           └─16372 openlitespeed (lshttpd - #02)

Jun 29 18:20:21 ip-172-31-22-219 systemd[1]: Starting OpenLiteSpeed HTTP Server...
Jun 29 18:20:22 ip-172-31-22-219 lswsctrl[16287]: [OK] litespeed: pid=16330.
Jun 29 18:20:24 ip-172-31-22-219 systemd[1]: Started OpenLiteSpeed HTTP Server.

```

## 02. Understand the process behind the port
---

```
ubuntu@ip-172-31-22-219:~$  sudo netstat -natulp | grep 7080
tcp        0      0 0.0.0.0:7080            0.0.0.0:*               LISTEN      16330/openlitespeed
udp        0      0 0.0.0.0:7080            0.0.0.0:*                           16330/openlitespeed

ubuntu@ip-172-31-22-219:~$ sudo systemctl list-units -t service --state=active| grep -i openlitespeed
lshttpd.service                                loaded active running OpenLiteSpeed HTTP Server
```

## 03. Understand the installed program for the port

```
ubuntu@ip-172-31-22-219:~$ sudo apt list --installed | grep -i openlitespeed
openlitespeed/bionic,now 1.7.11-1+bionic amd64 [installed]

```

## 04. Stop and Disable the service for the port
---



ubuntu@ip-172-31-22-219:~$ sudo systemctl stop lshttpd.service
ubuntu@ip-172-31-22-219:~$ sudo systemctl status lshttpd.service
● lshttpd.service - OpenLiteSpeed HTTP Server
   Loaded: loaded (/etc/systemd/system/lshttpd.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since Tue 2021-06-29 18:37:22 UTC; 12s ago
  Process: 12573 ExecStop=/usr/local/lsws/bin/lswsctrl delay-stop (code=exited, status=0/SUCCESS)
  Process: 16287 ExecStart=/usr/local/lsws/bin/lswsctrl start (code=exited, status=0/SUCCESS)
 Main PID: 16330
   CGroup: /system.slice/lshttpd.service

ubuntu@ip-172-31-22-219:~$ sudo systemctl disable lshttpd.service
Removed /etc/systemd/system/lsws.service.
Removed /etc/systemd/system/openlitespeed.service.
Removed /etc/systemd/system/multi-user.target.wants/lshttpd.service.
ubuntu@ip-172-31-22-219:~$ sudo systemctl status lshttpd.service
● lshttpd.service - OpenLiteSpeed HTTP Server
   Loaded: loaded (/etc/systemd/system/lshttpd.service; disabled; vendor preset: enabled)
   Active: inactive (dead)

## 05. Remove the program for the port
---

```
ubuntu@ip-172-31-22-219:~$ sudo apt-get remove openlitespeed
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages were automatically installed and are no longer required:
  aspell aspell-en dictionaries-common emacsen-common enchant hunspell-en-us libaspell15 libc-client2007e libenchant1c2a libhunspell-1.6-0 libjpeg-turbo8 libjpeg8 libsodium23 libwebp6
  libxpm4 libzip4 linux-aws-5.4-headers-5.4.0-1048 lsphp73 lsphp73-common lsphp73-imap lsphp73-json lsphp73-mysql lsphp73-opcache mlock php-common php-readline php7.2-common
  php7.2-readline rcs
Use 'sudo apt autoremove' to remove them.
The following packages will be REMOVED:
  openlitespeed
0 upgraded, 0 newly installed, 1 to remove and 39 not upgraded.
After this operation, 31.1 MB disk space will be freed.
N: Ignoring file 'devel:kubic:libcontainers:stable:cri-o:18.04.5' in directory '/etc/apt/sources.list.d/' as it has an invalid filename extension
Do you want to continue? [Y/n] Y
(Reading database ... 132297 files and directories currently installed.)
Removing openlitespeed (1.7.11-1+bionic) ...

ubuntu@ip-172-31-22-219:~$ sudo netstat -natulp | grep 7080

```
