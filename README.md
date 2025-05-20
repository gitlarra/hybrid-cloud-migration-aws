# Migration from On-Premises Infrastructure to Hybrid Architecture on AWS

This project documents the complete technical process of migrating from a traditional on-premises infrastructure to a hybrid architecture using AWS cloud services. Simulated in an academic environment, it represents a realistic case study of digital transformation for a growing small business.

---

## 🧠 Objective

Design, deploy, and document a hybrid architecture that retains critical services locally while leveraging the scalability and availability of the cloud for databases, web hosting, and shared storage.

---

## ⚙️ Technologies and Services Used

### On-Premises (simulated with ESXi)
- Active Directory (domain)
- SQL Server
- Simulated physical infrastructure (routers, VLANs, etc.)

### AWS Cloud
- **EC2**: Web server hosting (Apache)
- **EFS**: Shared file system between instances
- **RDS (SQL Server)**: Remotely accessible database
- **S3**: Manual backups and snapshot storage
- **EBS**: Disk expansion for Domain Controller
- **Application Load Balancer**: Load balancing between instances
- **VPC** with public/private subnets, 3 availability zones

---

## 📐 Architecture

> See diagram in the images folder

- On-prem zone: Users connected to Active Directory + Windows client
- Cloud zone: Web apps, databases, and backups accessible from anywhere
- Bastion Host: Secure access bridge to services in AWS private subnets

---

## 🛠️ Key Implementation Highlights

1. **Database migration**
   - SQL Server backup via script
   - Restoration in RDS
   - Enhanced security (access only via Bastion Host)

2. **Distributed web deployment**
   - 2 EC2 instances with Apache in different availability zones
   - EFS system to share web content
   - Load Balancer with public DNS

3. **Network and security**
   - Custom VPC
   - Security groups with minimal required rules
   - Private and public subnets with secure architecture

4. **Bastion Host + Domain Controller**
   - Controlled RDP access
   - EBS volume attached (100GB) for additional storage

---