# Cloud-Native runtime security - Falco

## A. Use Case
---

Whenever I exec to a Pod and open a shell - I want an alert to be generated. 


## B. Implementation 
---

### Install Falco 
---

I have installed Falco as a Linux service, documentation is available here.
https://falco.org/docs/getting-started/installation/

```
curl -s https://falco.org/repo/falcosecurity-3672BA8F.asc | apt-key add -
echo "deb https://download.falco.org/packages/deb stable main" | tee -a /etc/apt/sources.list.d/falcosecurity.list
apt-get update -y
apt-get -y install linux-headers-$(uname -r)
apt-get install -y falco

ubuntu@ip-172-31-17-89:~$ sudo systemctl start  falco
ubuntu@ip-172-31-17-89:~$ sudo systemctl status falco
● falco.service - Falco: Container Native Runtime Security
   Loaded: loaded (/usr/lib/systemd/system/falco.service; disabled; vendor preset: enabled)
   Active: active (running) since Wed 2021-06-16 08:15:32 UTC; 10s ago
     Docs: https://falco.org/docs/
  Process: 11322 ExecStartPre=/sbin/modprobe falco (code=exited, status=0/SUCCESS)
 Main PID: 11340 (falco)
    Tasks: 6 (limit: 4686)
   CGroup: /system.slice/falco.service
           └─11340 /usr/bin/falco --pidfile=/var/run/falco.pid

Jun 16 08:15:32 ip-172-31-17-89 falco[11340]: Falco initialized with configuration file /etc/falco/falco.yaml
Jun 16 08:15:32 ip-172-31-17-89 falco[11340]: Loading rules from file /etc/falco/falco_rules.yaml:
Jun 16 08:15:32 ip-172-31-17-89 falco[11340]: Wed Jun 16 08:15:32 2021: Falco initialized with configuration file /etc/falco/falco.yaml
Jun 16 08:15:32 ip-172-31-17-89 falco[11340]: Wed Jun 16 08:15:32 2021: Loading rules from file /etc/falco/falco_rules.yaml:
Jun 16 08:15:32 ip-172-31-17-89 falco[11340]: Loading rules from file /etc/falco/falco_rules.local.yaml:
Jun 16 08:15:32 ip-172-31-17-89 falco[11340]: Wed Jun 16 08:15:32 2021: Loading rules from file /etc/falco/falco_rules.local.yaml:
Jun 16 08:15:33 ip-172-31-17-89 falco[11340]: Loading rules from file /etc/falco/k8s_audit_rules.yaml:
Jun 16 08:15:33 ip-172-31-17-89 falco[11340]: Wed Jun 16 08:15:33 2021: Loading rules from file /etc/falco/k8s_audit_rules.yaml:
Jun 16 08:15:33 ip-172-31-17-89 falco[11340]: Starting internal webserver, listening on port 8765
Jun 16 08:15:33 ip-172-31-17-89 falco[11340]: Wed Jun 16 08:15:33 2021: Starting internal webserver, listening on port 8765


```

## C. Implementation 
---
