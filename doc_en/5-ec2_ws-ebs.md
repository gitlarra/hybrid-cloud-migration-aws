# 🪟 Windows Server + EBS

This section details the deployment of a Windows Server instance in a private subnet, configured as a Domain Controller (DC), along with the attachment and setup of an additional EBS volume to extend its storage.

> ⚠️ This was implemented in an AWS Academy lab environment. Due to restrictions (e.g. no IAM roles or snapshot automation), some real-world features were simulated manually.

---

## 🧭 Context and Objectives

The original setup included a local Domain Controller with limited scalability and no backup solution. The goal of this deployment was to:

- Host a Windows Server acting as a DC in a **private subnet**
- Allow RDP access **only from the Bastion Host**
- Attach additional storage (EBS volume) to simulate domain data or user profile storage

---

## 🖥️ Windows Server Configuration

1. **Launched EC2 Instance**:
   - OS: Microsoft Windows Server 2019
   - Subnet: Private subnet in `company-vpc`
   - Auto-assign public IP: Disabled
   - Key Pair: Same as Bastion (RDP access routed internally)

2. **Security Group: SG-WindowsDC**
   - Port 3389 allowed only from `SG-Bastion`

3. **RDP Access**:
   - Accessed by first connecting to the Bastion Host
   - Used internal private IP from there to RDP into Windows Server

4. **Active Directory Role**:
   - The instance was promoted to Domain Controller
   - Domain was created and joined from a client machine via internal test

---

## 💾 EBS Volume Attachment

1. **Created a new EBS volume**:
   - Size: 100 GB
   - Type: General Purpose SSD (gp2)
   - Availability Zone: Same as the Windows Server instance

2. **Attached the volume** via AWS Console:
   - Device: `/dev/sdf` (automatically becomes `D:` in Windows)

3. **Volume Initialization** in Windows:
   - Logged in via RDP from Bastion Host
   - Opened Disk Management
   - Initialized the new disk
   - Created and formatted a new volume as drive `D:`
   - Labeled the drive (e.g., `DomainData`)

> 📌 This setup simulates having a separate storage volume for domain profiles, logs, or backups.

---

## ✅ Outcome

- Windows Server instance hosted securely in a private subnet
- RDP access only possible through Bastion Host
- Domain Controller role configured and operational
- Additional EBS volume mounted and usable as persistent storage

Next: [S3 (Backups) ➜](6-S3.md)
