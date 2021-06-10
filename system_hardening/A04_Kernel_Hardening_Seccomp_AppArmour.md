# Kernel Hardening using AppArmor & Seccomp

## Seccomp
---

Check if Seccomp is enabled by default in the Kernel, first get the kernel version and then check seccomp

```
ubuntu@ip-172-31-22-219:~$ uname -r
5.4.0-1049-aws
ubuntu@ip-172-31-22-219:~$ grep -i seccomp /boot/config-$(uname -r)
CONFIG_SECCOMP=y
CONFIG_HAVE_ARCH_SECCOMP_FILTER=y
CONFIG_SECCOMP_FILTER=y

```

When any process runs - we can check the Seccomp mode, which can have 3 values, 

- Mode 0 - DISABLED
- Mode 1 - STRICT 
- Mode 2 - FILTERED

The way to check seccomp for a process would be to get the PID of the process, the then check the process namespace and status. 
Lets take a process

root      1334   855  0 16:57 ?        00:00:00 /snap/amazon-ssm-agent/3552/ssm-agent-worker

This process has a PID file of 1334. 

```
ubuntu@ip-172-31-22-219:~$ sudo cat /proc/1334/status | grep -i Seccomp
Seccomp:        0
```
So here its clear the process is running with a Seccomp mode of  0 - disabled. 

Default Docker seccomp profile restricts 60 
docker --security-opt

