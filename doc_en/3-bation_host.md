# 🔐 Bastion Host

This section covers the deployment and configuration of a Bastion Host (jump box) in AWS. Its purpose is to serve as the only secure entry point into the private subnet where internal services such as RDS and Windows Server Domain Controller are hosted.

> ⚠️ This was configured within the AWS Academy lab environment, which restricts the use of IAM roles and VPNs. For that reason, public RDP access was temporarily allowed (with security group restrictions) to simulate secure access in a lab environment.

---

## 🧭 Context and Objectives

A Bastion Host is a fundamental component in hybrid architectures where public access to internal services must be avoided.

Goals of this setup:
- Enable secure RDP access to resources inside private subnets
- Avoid exposing internal services (RDS, DC) directly to the internet
- Allow future connections from an on-premises environment or VPN tunnel (if available)

---

## 🖥️ Instance Configuration

1. **Launched a Windows EC2 instance** in a **public subnet**
   - Operating System: Microsoft Windows Server 2019 Base
   - Instance type: `t2.medium`
   - Enabled auto-assign public IP
   - AMI provided by AWS Academy lab

2. **Associated Security Group:** `SG-Bastion`
   - Allowed RDP (TCP 3389) from `0.0.0.0/0` (lab scenario)
   - Used only temporarily and should be restricted in production

3. **Access:**
   - Connected using RDP via the AWS-provided `.pem` key
   - Used as an intermediate point to connect to:
     - RDS (SQL Server)
     - EC2 Windows Domain Controller

---

## 🔐 Security Group Recap (SG-Bastion)

| Protocol | Port  | Source     | Purpose                       |
|----------|-------|------------|-------------------------------|
| TCP      | 3389  | 0.0.0.0/0  | RDP access to Bastion Host    |

Once connected, further RDP or SQL connections are made **internally**, from the Bastion to private subnet targets.

---

## ✅ Outcome

- A secure jump box was established for internal administration
- Access to RDS and Domain Controller was confirmed via RDP
- No direct public exposure was required for private resources

Next: [RDS (SQL Server) ➜](4-rds.md)