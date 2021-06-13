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

Even a simple Unix command creates a lot of syscall with we can view with ptrace - the echo command makes syscalls like execve,mmap etc etc

```
ubuntu@ip-172-31-22-219:~$ strace echo "hello world"

execve("/bin/echo", ["echo", "hello world"], 0x7ffd4828e198 /* 20 vars */) = 0
brk(NULL)                               = 0x562a515b7000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=22672, ...}) = 0
mmap(NULL, 22672, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f0351f90000
close(3)                                = 0
....
...
fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 0), ...}) = 0
write(1, "hello world\n", 12hello world
)           = 12
close(1)                                = 0
close(2)                                = 0
exit_group(0)                           = ?
+++ exited with 0 +++



```

In Kubernetes - we need to create a JSON file - which acts like the secccomp grammer (also called seccomp profile) and keep it at each node /var/lib/kubelet/seccomp/profiles
Then the support is available via securityContext - where we can load the profile via the hostpath. To help the situation, Kubernetes has a default profile created for us (we don't have to custom develop the JSON - and we can use runtime default

```
securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/violation.json

securityContext:
    seccompProfile:
      type: RuntimeDefault
```

