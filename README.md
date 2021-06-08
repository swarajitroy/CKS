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

## D. Minimize Microservices Vulnerabilities (20%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Setup appropriate OS Leve security domains - PodSecurityPolicies, OPA, SecurityContext](https://github.com/swarajitroy/CKS/blob/main/minimize_microservice_vulnerability/A01_SC_PSP_OPA.md) |   |
| 02 | Manage Kubernetes Secrets |   |
| 03 | Use Container runtime sandboxes - gvisor, kata containers |   |
| 04 | Implement pod to pod encryption - mTLS |   |

## E. Supply Chain Security (20%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Minimize Base image footprint](https://github.com/swarajitroy/CKS/blob/main/supply_chain_security/A01_reduce_base_image_footprint.md) |   |
| 02 | [Whitelisting allowed image registry via ImagePolicyWebhook](https://github.com/swarajitroy/CKS/blob/main/supply_chain_security/A02_Whitelisting_Image_Registries.md)|   |
| 03 | Whitelisting allowed image registry via OPA |   |
| 04 | Use Static Analysis of user workloads (KubeSec) - Kubernetes Manifests and Docker files |   |
| 05 | Use Static Analysis of user workloads (ConfTest) - Kubernetes Manifests and Docker files |   |
| 06 | Scan Images for known Vulnerability (Trivy) |   |

## F. Monitoring, Logging & Runtime Security  (20%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
