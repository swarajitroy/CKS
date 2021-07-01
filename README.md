# CKS

## A. Cluster Setup (10%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Use Network Security Policies](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A01_NetworkPolicy.md) |  https://github.com/ahmetb/kubernetes-network-policy-recipes |
| 02 | [Use CIS Benchmarks (tool KubeBench)](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A02_Kubebench.md) | https://github.com/aquasecurity/kube-bench |
| 03 | [Properly setup Ingress objects with Security control](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A03_Ingress_Security_Control.md) ||
| 04 | [Protect node metadata and endpoints](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A04_protect_node_metadata.md)|| 
| 05 | [Minimize use of and access to GUI elements](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A05_MinimizeGUI.md) ||
| 06 | [Verify platform binaries before deploying](https://github.com/swarajitroy/CKS/blob/main/cluster_setup/A06_VerifyPlatformBinaries.md) ||

## B. Cluster Hardening (15%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Use Role Based access control (RBAC) to minimize exposure](https://github.com/swarajitroy/CKS/blob/main/cluster_hardening/A01_RBAC.md) |   |
| 02 | [Restrict access to Kubernetes API](https://github.com/swarajitroy/CKS/blob/main/cluster_hardening/A02_RestrictAPIAccess.md) | |
| 03 | [Excercise caution in using ServiceAccounts e.g disable defaults, minimize permissions on newly created ones](https://github.com/swarajitroy/CKS/blob/main/cluster_hardening/A03_ServiceAccountDefaults.md) | |
| 04 | [Update Kubernetes Frequently](https://github.com/swarajitroy/CKS/blob/main/cluster_hardening/A04_UpgradeK8s.md) | |

## C. System Hardening (15%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Minimize host OS footprint (reduce attack surface)](https://github.com/swarajitroy/CKS/blob/main/system_hardening/A01_ReduceAttackSurface.md)  | |
| 02 | Minimize IAM Roles |   |
| 03 | Minimize external access to the network |   |
| 04 | [Appropritately use Kernel hardening tools such as AppArmor, Seccomp](https://github.com/swarajitroy/CKS/blob/main/system_hardening/A04_Kernel_Hardening_Seccomp_AppArmour.md) |   |

## D. Minimize Microservices Vulnerabilities (20%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Setup appropriate OS Leve security domains - PodSecurityPolicies, OPA, SecurityContext](https://github.com/swarajitroy/CKS/blob/main/minimize_microservice_vulnerability/A01_SC_PSP_OPA.md) |   |
| 02 | [Manage Kubernetes Secrets](https://github.com/swarajitroy/CKS/blob/main/minimize_microservice_vulnerability/A02_ManageSecrets.md) |   |
| 03 | [Use Container runtime sandboxes - gvisor, kata containers](https://github.com/swarajitroy/CKS/blob/main/minimize_microservice_vulnerability/A03_1_GVisor.md) |   |
| 04 | Implement pod to pod encryption - mTLS |   |

## E. Supply Chain Security (20%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Minimize Base image footprint](https://github.com/swarajitroy/CKS/blob/main/supply_chain_security/A01_reduce_base_image_footprint.md) |   |
| 02 | [Whitelisting allowed image registry via ImagePolicyWebhook](https://github.com/swarajitroy/CKS/blob/main/supply_chain_security/A02_Whitelisting_Image_Registries.md)|   |
| 03 | [Use Static Analysis of user workloads (KubeSec) - Kubernetes Manifests and Docker files](https://github.com/swarajitroy/CKS/blob/main/supply_chain_security/A04_StaticAnalysis_Kubesec.md) |   |
| 04 | Use Static Analysis of user workloads (ConfTest) - Kubernetes Manifests and Docker files |   |
| 05 | [Scan Images for known Vulnerability (Trivy)](https://github.com/swarajitroy/CKS/blob/main/supply_chain_security/A05_ImageVulnerabilityScan_Trivy.md) |   |

## F. Monitoring, Logging & Runtime Security  (20%)
---
| ID | Topic | Remarks |
| ----------- | ----------- | ----------- |
| 01 | [Runtime Security - Behaviour Analysis of Container and Hosts via Falco](https://github.com/swarajitroy/CKS/blob/main/monitoring_logging_runtime_security/A01_Falco.md) |   |
| 02 | [Runtime Security - Auditing](https://github.com/swarajitroy/CKS/blob/main/monitoring_logging_runtime_security/A02_Audit_Logging.md) ||
| 03 | [Runtime Security - Immutable Containers](https://github.com/swarajitroy/CKS/blob/main/monitoring_logging_runtime_security/A03_ImmutableContainer.md) ||


