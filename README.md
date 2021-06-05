# CKS

## A. Cluster Setup (10%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | Use Network Security Policies |  https://github.com/ahmetb/kubernetes-network-policy-recipes |
| 02 | Use CIS Benchmarks (tool KubeBench) | https://github.com/aquasecurity/kube-bench |
| 03 | [Properly setup Ingress objects with Security control](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A03_Ingress_Security_Control.md) ||
| 04 | [Protect node metadata and endpoints](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A04_protect_node_metadata.md)|| 
| 05 | Minimize use of and access to GUI elements ||
| 06 | Verify platform binaries before deploying ||

## B. Cluster Hardening (15%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | Use Role Based access control (RBAC) to minimize exposure |   |
| 02 | Restrict access to Kubernetes API | |
| 03 | Excercise caution in using ServiceAccounts e.g disable defaults, minimize permissions on newly created ones | |
| 04 | Update Kubernetes Frequently | |

## C. System Hardening (15%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | Minimize host OS footprint (reduce attack surface) |   |
| 02 | Minimize IAM Roles |   |
| 03 | Minimize external access to the network |   |
| 04 | Appropritately use Kernel hardening tools such as AppArmor, Seccomp |   |

