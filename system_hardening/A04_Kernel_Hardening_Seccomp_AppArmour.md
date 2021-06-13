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

Lets run a nginx pod with a seccomp profile. 

Create the profile and place it at /var/lib/kubelet/seccomp/profiles/swararoy-seccomp.json on the worker node. 

```
ubuntu@ip-172-31-17-89:/var/lib$ sudo cat /var/lib/kubelet/seccomp/profiles/swararoy-seccomp.json
{
    "defaultAction": "SCMP_ACT_ERRNO",
    "architectures": [
        "SCMP_ARCH_X86_64",
        "SCMP_ARCH_X86",
        "SCMP_ARCH_X32"
    ],
    "syscalls": [
        {
            "names": [
                "accept4",
                "epoll_wait",
                "pselect6",
                "futex",
                "madvise",
                "epoll_ctl",
                "getsockname",
                "setsockopt",
                "vfork",
                "mmap",
                "read",
                "write",
                "close",
                "arch_prctl",
                "sched_getaffinity",
                "munmap",
                "brk",
                "rt_sigaction",
                "rt_sigprocmask",
                "sigaltstack",
                "gettid",
                "clone",
                "bind",
                "socket",
                "openat",
                "readlinkat",
                "exit_group",
                "epoll_create1",
                "listen",
                "rt_sigreturn",
                "sched_yield",
                "clock_gettime",
                "connect",
                "dup2",
                "epoll_pwait",
                "execve",
                "exit",
                "fcntl",
                "getpid",
                "getuid",
                "ioctl",
                "mprotect",
                "nanosleep",
                "open",
                "poll",
                "recvfrom",
                "sendto",
                "set_tid_address",
                "setitimer",
                "writev"
            ],
            "action": "SCMP_ACT_ALLOW"
        }
    ]
}


```

Now lets create the pod definition 

```
ubuntu@ip-172-31-22-219:~/seccomp-practice$ cat seccomp-test-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: seccomp-test-pod
  name: seccomp-test-pod
spec:
  securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/swararoy-seccomp.json
  containers:
  - image: nginx:1.21.0
    name: seccomp-test-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```
and run the pod

```
ubuntu@ip-172-31-22-219:~/seccomp-practice$ kubectl create -f seccomp-test-pod.yaml
Error from server (Forbidden): error when creating "seccomp-test-pod.yaml": pods "seccomp-test-pod" is forbidden: PodSecurityPolicy: unable to admit pod: [pod.metadata.annotations[seccomp.security.alpha.kubernetes.io/pod]: Forbidden: seccomp may not be set pod.metadata.annotations[container.seccomp.security.alpha.kubernetes.io/seccomp-test-pod]: Forbidden: seccomp may not be set]

```
To solve this error - ensure the PodSecurityPolicy you have allows Seccomp and allows custom seccomp profiles

```
ubuntu@ip-172-31-22-219:~/podsecuritypolicy$ cat psp.yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: example
  annotations:
    seccomp.security.alpha.kubernetes.io/defaultProfileName: runtime/default
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: '*'

```

## Apparmor
---

In each worker node, check following to inspect the AppArmor setup. 

- AppArmor is enabled, AppArmor service is running

```
ubuntu@ip-172-31-17-89:~$ cat /sys/module/apparmor/parameters/enabled
Y

ubuntu@ip-172-31-17-89:~$ sudo systemctl status apparmor
● apparmor.service - AppArmor initialization
   Loaded: loaded (/lib/systemd/system/apparmor.service; enabled; vendor preset: enabled)
   Active: active (exited) since Sun 2021-06-13 04:52:21 UTC; 8h ago
     Docs: man:apparmor(7)
           http://wiki.apparmor.net/
  Process: 463 ExecStart=/etc/init.d/apparmor start (code=exited, status=0/SUCCESS)
 Main PID: 463 (code=exited, status=0/SUCCESS)

Jun 13 04:52:21 ip-172-31-17-89 apparmor[463]:  * Starting AppArmor profiles
Jun 13 04:52:21 ip-172-31-17-89 apparmor[463]: Skipping profile in /etc/apparmor.d/disable: usr.sbin.rsyslogd
Jun 13 04:52:21 ip-172-31-17-89 apparmor[463]:    ...done.
Jun 13 04:52:21 ip-172-31-17-89 systemd[1]: Starting AppArmor initialization...
Jun 13 04:52:21 ip-172-31-17-89 systemd[1]: Started AppArmor initialization.

```

- AppArmor profiles are loaded - which are in enforcement and which are in complaining mode

```
ubuntu@ip-172-31-17-89:~$ sudo aa-status
apparmor module is loaded.
23 profiles are loaded.
21 profiles are in enforce mode.
   /sbin/dhclient
   /snap/snapd/11841/usr/lib/snapd/snap-confine
   /snap/snapd/11841/usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /snap/snapd/12057/usr/lib/snapd/snap-confine
   /snap/snapd/12057/usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/bin/lxc-start
   /usr/bin/man
   /usr/lib/NetworkManager/nm-dhcp-client.action
   /usr/lib/NetworkManager/nm-dhcp-helper
   /usr/lib/connman/scripts/dhclient-script
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/sbin/tcpdump
   crio-default
   lxc-container-default
   lxc-container-default-cgns
   lxc-container-default-with-mounting
   lxc-container-default-with-nesting
   man_filter
   man_groff
   snap-update-ns.amazon-ssm-agent
2 profiles are in complain mode.
   snap.amazon-ssm-agent.amazon-ssm-agent
   snap.amazon-ssm-agent.ssm-cli
44 processes have profiles defined.
42 processes are in enforce mode.
   crio-default (807)
   ...
   crio-default (5957)
   crio-default (10934)
2 processes are in complain mode.
   snap.amazon-ssm-agent.amazon-ssm-agent (869)
   snap.amazon-ssm-agent.amazon-ssm-agent (1240)
0 processes are unconfined but have a profile defined.

```

Now let us define an use case where we want pods not to be able to write to a particular file systems. One way to acheive this would be, 

- Create an AppArmor profile named swararoy-apparmor-deny-write
- Copy the profile to all Kubernetes worker nodes into default apparmor profile directory
- Load the profile into Enforcement mode
- Create a Pod (busybox with sleep command) with apparmor swararoy-apparmor-deny-write profile loaded
- Exec to the pod and check if the write is getting allowed 
