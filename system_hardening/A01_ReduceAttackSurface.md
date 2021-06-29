
Intall Openlitespeed

https://openlitespeed.org/kb/install-ols-from-litespeed-repositories/

```
ubuntu@ip-172-31-22-219:~$ sudo wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh | sudo bash
--2021-06-29 18:19:24--  http://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh
Resolving rpms.litespeedtech.com (rpms.litespeedtech.com)... 52.55.120.73
Connecting to rpms.litespeedtech.com (rpms.litespeedtech.com)|52.55.120.73|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3644 (3.6K) [application/x-sh]
Saving to: ‘STDOUT’

-                                               100%[====================================================================================================>]   3.56K  --.-KB/s    in 0s

2021-06-29 18:19:24 (386 MB/s) - written to stdout [3644/3644]

 detecting OS type :
detected OS: ubuntu - 18.04
 now enable the LiteSpeed Debian Repo
 register LiteSpeed GPG key
--2021-06-29 18:19:25--  http://rpms.litespeedtech.com/debian/lst_debian_repo.gpg
Resolving rpms.litespeedtech.com (rpms.litespeedtech.com)... 52.55.120.73
Connecting to rpms.litespeedtech.com (rpms.litespeedtech.com)|52.55.120.73|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1198 (1.2K) [application/octet-stream]
Saving to: ‘/etc/apt/trusted.gpg.d/lst_debian_repo.gpg’

/etc/apt/trusted.gpg.d/lst_debian_repo.gpg      100%[====================================================================================================>]   1.17K  --.-KB/s    in 0s

2021-06-29 18:19:25 (160 MB/s) - ‘/etc/apt/trusted.gpg.d/lst_debian_repo.gpg’ saved [1198/1198]

--2021-06-29 18:19:25--  http://rpms.litespeedtech.com/debian/lst_repo.gpg
Resolving rpms.litespeedtech.com (rpms.litespeedtech.com)... 52.55.120.73
Connecting to rpms.litespeedtech.com (rpms.litespeedtech.com)|52.55.120.73|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2336 (2.3K) [application/octet-stream]
Saving to: ‘/etc/apt/trusted.gpg.d/lst_repo.gpg’

/etc/apt/trusted.gpg.d/lst_repo.gpg             100%[====================================================================================================>]   2.28K  --.-KB/s    in 0s

2021-06-29 18:19:25 (280 MB/s) - ‘/etc/apt/trusted.gpg.d/lst_repo.gpg’ saved [2336/2336]

 update the repo
Hit:1 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic InRelease
Hit:2 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic-updates InRelease
Hit:3 http://us-east-2.ec2.archive.ubuntu.com/ubuntu bionic-backports InRelease
Hit:4 http://security.ubuntu.com/ubuntu bionic-security InRelease
Ign:5 http://rpms.litespeedtech.com/debian bionic InRelease
Get:6 http://rpms.litespeedtech.com/debian bionic Release [1653 B]
Get:7 http://rpms.litespeedtech.com/debian bionic Release.gpg [836 B]
Hit:9 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.20/xUbuntu_18.04  InRelease
Get:8 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [9383 B]
Hit:10 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_18.04  InRelease
Get:11 http://rpms.litespeedtech.com/debian bionic/main amd64 Packages [22.4 kB]
Fetched 34.3 kB in 1s (50.0 kB/s)
Reading package lists... Done
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
