# 🗄️ RDS (SQL Server)

This section documents the deployment of a Microsoft SQL Server instance using Amazon RDS, its secure configuration, and the migration of the existing on-premises database.

> ⚠️ This setup was deployed within the AWS Academy lab environment, which restricts the use of IAM roles, automated backups, and snapshot sharing. Access was configured to simulate a real-world scenario, but with certain limitations due to the lab context.

---

## 🧭 Context and Objectives

Originally, the SQL Server database was hosted in the on-premises environment with limited accessibility and performance issues. The goal of this migration was to:

- Host the database on a cloud-managed service (RDS)
- Maintain secure access limited to internal users (via Bastion Host)
- Improve availability and performance
- Preserve structure and data during migration

---

## 🗂️ RDS Deployment

1. **Created an RDS instance** using SQL Server Express Edition
   - Instance class: `db.t3.micro`
   - Storage: 20 GB
   - VPC: `company-vpc`
   - Subnet group: Private subnet (one AZ only)
   - Public accessibility: **Disabled**
   - Backup: Not configurable in the lab environment

2. **Security Group: SG-RDS**
   - Port: 1433 (SQL Server)
   - Source: Only `SG-Bastion`

3. **Subnet and access**:
   - Deployed in a **private subnet**
   - Could not be accessed from the internet directly
   - Connectivity confirmed via Bastion Host

---

## 🔁 Database Migration

Due to IAM restrictions in the AWS Academy lab, standard migration tools like AWS Database Migration Service (DMS) were unavailable.

Instead, a **manual migration** was done:

1. Backed up the on-prem SQL Server database to a `.bak` file
2. Converted the backup to a `.sql` script using SQL Server Management Studio (SSMS)
3. Transferred the script to the Bastion Host
4. Connected to the RDS instance via SSMS (from inside the Bastion)
5. Ran the script to recreate schema and insert data

> 📌 Note: This method works well for small databases and lab setups, but should be replaced with automated migration pipelines in production.

---

## ✅ Outcome

- The database was successfully restored and validated in RDS
- Access is restricted via security groups and private networking
- Availability, durability, and scalability were improved over the on-prem setup

Next: [Windows Server + EBS ➜](windows-ebs.md)
