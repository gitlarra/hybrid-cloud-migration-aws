# 🗄️ RDS (SQL Server)

Esta sección documenta el despliegue de una instancia de Microsoft SQL Server usando Amazon RDS, su configuración segura y la migración de la base de datos que originalmente estaba on-premises.

> ⚠️ Esta configuración se realizó dentro de un entorno de laboratorio de AWS Academy, el cual restringe el uso de roles de IAM, backups automáticos y el uso compartido de snapshots. El acceso fue configurado para simular un escenario real, aunque con ciertas limitaciones propias del entorno de laboratorio.

---

## 🧭 Contexto y Objetivos

Originalmente, la base de datos SQL Server se alojaba en un entorno on-premises con accesibilidad limitada y problemas de rendimiento. El objetivo de esta migración fue:

- Alojar la base de datos en un servicio gestionado en la nube (RDS)
- Mantener el acceso seguro limitado a usuarios internos (a través del Bastion Host)
- Mejorar la disponibilidad y el rendimiento
- Preservar la estructura y los datos durante la migración

---

## 🗂️ Despliegue de RDS

1. **Se creó una instancia RDS** utilizando SQL Server Express Edition
   - Clase de instancia: `db.t3.micro`
   - Almacenamiento: 20 GB
   - VPC: `company-vpc`
   - Grupo de subredes: Subred privada (solo una AZ)
   - Accesibilidad pública: **Desactivada**
   - Backup: No configurable en el entorno de laboratorio por falat de roles IAM

2. **Grupo de Seguridad: SG-RDS**
   - Puerto: 1433 (SQL Server)
   - Origen: Solo `SG-Bastion`

3. **Subred y acceso**:
   - Desplegada en una **subred privada**
   - No accesible directamente desde Internet
   - Conectividad confirmada a través del Bastion Host

---

## 🔁 Migración de la Base de Datos

Debido a las restricciones de IAM en el entorno de laboratorio de AWS Academy, herramientas de migración estándar como AWS Database Migration Service (DMS) no estaban disponibles.

En su lugar, se realizó una **migración manual**:

1. Se generó un backup de la base de datos on-premises en un archivo `.bak`
2. Se convirtió ese backup a un script `.sql` usando SQL Server Management Studio (SSMS)
3. El script se transfirió al Bastion Host
4. Se accedió a la instancia RDS desde SSMS (desde dentro del Bastion)
5. Se ejecutó el script para recrear el esquema e insertar los datos

> 📌 Nota: Este método funciona bien para bases de datos pequeñas y entornos de laboratorio, pero debe reemplazarse por pipelines de migración automatizados en producción.

---

## ✅ Resultado

- La base de datos fue restaurada con éxito y validada en RDS
- El acceso está restringido mediante grupos de seguridad y red privada
- La disponibilidad, durabilidad y escalabilidad mejoraron respecto al entorno on-premises

Siguiente: [Windows Server + EBS ➜](5-ec2_ws-ebs-es.md)
