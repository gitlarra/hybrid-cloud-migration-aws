# 🖥️ EC2 + EFS + Load Balancer

This document details the setup of the EC2 instances, shared storage with EFS, and the configuration of the Application Load Balancer (ALB). These components were designed to serve the web content in a scalable and redundant way across multiple availability zones.

> ⚠️ This was deployed using an AWS Academy lab environment, which limits the use of IAM roles and advanced automation. Public access rules like `0.0.0.0/0` are used only for lab purposes and would not be recommended in production.

---

## 🧭 Context and Objectives

The on-premises infrastructure hosted static websites on local servers. The goal of this migration was to:

- Deploy redundant web servers in the cloud
- Share a single content directory across multiple EC2s
- Route traffic through a centralized load balancer
- Ensure users can access the web from anywhere via a public DNS

---

## 🖥️ EC2 Web Servers

1. **Launched 2 EC2 instances** (Debian 11) in different Availability Zones
2. Installed required packages:
   - Apache2
   - NFS Common (for EFS mounting)
3. Created a directory for web content:  
   `/var/www/html/efs`

---

## 🔗 Elastic File System (EFS)

- Created a new EFS file system in the same VPC
- Created a mount target in each Availability Zone
- Associated the SG-EFS security group
- On each EC2:
  - Mounted the EFS volume on `/var/www/html/efs`
  - Verified that file changes reflected across both instances
  - Made mount persistent via `/etc/fstab`

> 💡 The EFS system ensures both EC2s serve identical content and can scale horizontally if needed.

---

## ⚙️ Apache Configuration

- Modified the Apache configuration file:
  - Updated the `DocumentRoot` to point to `/var/www/html/efs`
- Restarted Apache service:
  ```bash
  sudo systemctl restart apache2

---

## ⚖️ Application Load Balancer (ALB)

- Created an **Application Load Balancer** in public subnets
- Security group: **SG-ELB**
- Created a **Target Group**:
  - Type: Instance
  - Protocol: HTTP
  - Port: 80
- Registered both EC2 instances
- Configured listener:
  - HTTP (port 80) → Forward to Target Group

> 🌐 The ALB provides a single public DNS to access both servers and balances traffic automatically.

---

## ✅ Outcome

- Two EC2 web servers are serving identical content
- Storage is shared and synced via EFS
- ALB handles external traffic and distributes it evenly
- Setup supports high availability and horizontal scaling

Next: [Bastion Host ➜](3-bastion_host.md)