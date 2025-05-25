# 🧱 VPC and Security Groups

This document describes the initial setup of the AWS Virtual Private Cloud (VPC) and the related security groups used to isolate, protect, and enable communication across the hybrid infrastructure. This is the foundation on which all cloud services were built.

> ⚠️ This setup was done using an AWS Academy lab environment, which has some limitations such as the absence of IAM roles and fixed public IP addresses. These constraints affect certain best practices, such as the use of 0.0.0.0/0 for security group rules.

---

## 🧭 Context and Objectives

Before deploying any service to AWS, it was essential to design a secure and scalable network. The goal was to divide the infrastructure into **public and private subnets** distributed across multiple Availability Zones (AZs), ensuring redundancy, segmentation, and strict access control.

Key goals:
- Enable isolated zones for public-facing and internal services
- Support RDS and Windows Server inside private subnets
- Secure access using Bastion Host only
- Prepare EFS and EC2 for web deployment

---

## 🌐 VPC Configuration

1. **Created a new VPC** via the AWS VPC Wizard
   - Name: `company-vpc`
   - CIDR block: `10.0.0.0/16` (allows a large number of IPs)

2. **Created 6 subnets** across 3 Availability Zones:
   - 3 **Public subnets** (2 for EC2 Web Servers, 1 for Bastion Host)
   - 3 **Private subnets** (1 for RDS and Windows Server DC, 2 unused)

3. **Disabled NAT Gateway** by default for security purposes
   - Can be temporarily enabled if Windows Server needs to update

4. **Enabled S3 Gateway** to allow private subnet resources to access S3 without internet exposure
   - Not in this case becasuse of the IAM roles

5. **Enabled Auto-assign Public IP for Public Subnets**
   - AWS requires this option to be set at subnet level for EC2 instances to get a public IP (even if DHCP is configured during EC2 creation)

---

## 🔐 Security Groups Setup

Security Groups (SGs) are attached to specific services and define traffic rules for each component. These were all created under the `company-vpc` context.

> 🔒 Outbound rules were left as default (all traffic allowed)

### SG-ELB (Application Load Balancer)
| Protocol | Port | Source     | Description         |
|----------|------|------------|---------------------|
| TCP      | 80   | 0.0.0.0/0  | Public HTTP access  |

### SG-WebServer (EC2 Web Servers)
| Protocol | Port | Source        | Description                  |
|----------|------|---------------|------------------------------|
| TCP      | 80   | SG-ELB        | HTTP traffic from ALB        |
| TCP      | 22   | 0.0.0.0/0     | SSH access for configuration |
| TCP      | 2049 | SG-EFS        | NFS (EFS mount sharing)      |

### SG-EFS (Elastic File System)
| Protocol | Port | Source        | Description                  |
|----------|------|---------------|------------------------------|
| TCP      | 2049 | SG-WebServer  | Allow EC2 to mount EFS       |

### SG-Bastion (Windows Bastion Host)
| Protocol | Port | Source     | Description              |
|----------|------|------------|--------------------------|
| TCP      | 3389 | 0.0.0.0/0  | RDP access from anywhere |

### SG-WindowsDC (Domain Controller)
| Protocol | Port | Source     | Description                    |
|----------|------|------------|--------------------------------|
| TCP      | 3389 | SG-Bastion | RDP access only via Bastion    |

### SG-RDS (SQL Server Database)
| Protocol | Port | Source     | Description                          |
|----------|------|------------|--------------------------------------|
| TCP      | 1433 | SG-Bastion | SQL Server access from Bastion Host  |

> 🧪 All SG configurations were validated through connection tests to confirm proper isolation and reachability only via designated paths.

---

## ✅ Outcome

- Subnets and routes prepared for next deployments (EC2, EFS, RDS)
- Public services (web) are isolated from internal services (DB, DC)
- Clear segmentation and zero-trust principle applied across components

Next: [EC2 + EFS + Load Balancer ➜](ec2-efs-alb.md)


---