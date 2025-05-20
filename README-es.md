# Migración de Infraestructura On-Premises a Arquitectura Híbrida en AWS

Este proyecto documenta el proceso técnico completo de una migración desde una infraestructura tradicional on-premises a una arquitectura híbrida utilizando servicios en la nube de AWS. Simulado en un entorno académico, representa un caso práctico realista de transformación digital para una PYME en crecimiento.

---

## 🧠 Objetivo

Diseñar, desplegar y documentar una arquitectura híbrida que mantenga servicios críticos localmente, y aproveche la escalabilidad y disponibilidad de la nube para bases de datos, web y almacenamiento compartido.

---

## ⚙️ Tecnologías y Servicios Utilizados

### On-Premises (simulado con ESXi)
- Active Directory (dominio)
- SQL Server
- Infraestructura física simulada (routers, VLANs, etc.)

### AWS Cloud
- **EC2**: Hosting de servidores web (Apache)
- **EFS**: Sistema de archivos compartido entre instancias
- **RDS (SQL Server)**: Base de datos accesible remotamente
- **S3**: Backup manual y almacenamiento de snapshots
- **EBS**: Ampliación de disco para Domain Controller
- **Application Load Balancer**: Balanceo entre instancias
- **VPC** con subredes públicas/privadas, 3 zonas de disponibilidad

---

## 📐 Arquitectura

> Ver diagrama en la carpeta imágenes

- Zona on-prem: Usuarios conectados a Active Directory + cliente en Windows
- Zona Cloud: Webs, BBDD y backups accesibles desde cualquier punto
- Bastion Host: Puente de acceso seguro a servicios en subred privada en AWS

---

## 🛠️ Implementación Destacada

1. **Migración de base de datos**
   - Backup de SQL Server en script
   - Restauración en RDS
   - Seguridad reforzada (acceso solo desde Bastion Host)

2. **Despliegue web distribuido**
   - 2 instancias EC2 con Apache en diferentes zonas de disponibilidad
   - Sistema EFS para compartir contenido web
   - Load Balancer con DNS público

3. **Red y Seguridad**
   - VPC personalizada
   - Grupos de seguridad con reglas mínimas necesarias
   - Subredes privadas y públicas con arquitectura segura

4. **Bastion Host + Domain Controller**
   - Acceso controlado mediante RDP
   - Asociación de volumen EBS adicional de 100GB

---