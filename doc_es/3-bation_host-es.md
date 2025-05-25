# 🔐 Bastion Host

Esta sección cubre el despliegue y configuración de un Bastion Host (bridge) en AWS. Su propósito es servir como el único punto de entrada seguro hacia la subred privada donde se alojan servicios internos como RDS y el Controlador de Dominio de Windows Server.

> ⚠️ Esta configuración se realizó en un entorno de laboratorio de AWS Academy, el cual restringe el uso de roles de IAM y VPNs. Por este motivo, se permitió temporalmente el acceso RDP público (con restricciones en el grupo de seguridad) para simular un acceso seguro en un entorno académico.

---

## 🧭 Contexto y Objetivos

Un Bastion Host es un componente fundamental en arquitecturas híbridas donde debe evitarse el acceso público directo a los servicios internos.

Objetivos de esta configuración:
- Habilitar acceso RDP seguro a recursos dentro de subredes privadas
- Evitar exponer servicios internos (RDS, DC) directamente a Internet
- Permitir futuras conexiones desde una red local o túnel VPN (si estuviera disponible)

---

## 🖥️ Configuración de la Instancia

1. **Se lanzó una instancia EC2 con Windows** en una **subred pública**
   - Sistema operativo: Microsoft Windows Server 2019 Base, lo ideal sería Linux pero por motivos de este contexto en concreto se usara Windows
   - Tipo de instancia: `t2.medium`
   - Asignación automática de IP pública activada
   - AMI proporcionada por el laboratorio de AWS Academy

2. **Grupo de Seguridad Asociado:** `SG-Bastion`
   - RDP (TCP 3389) permitido desde `0.0.0.0/0` (solo en entorno de laboratorio, no es recomendable en una situación real)
   - Usado únicamente de forma temporal; en producción debería restringirse

3. **Acceso:**
   - Conexión mediante RDP utilizando la clave `.pem` proporcionada por AWS
   - Utilizado como punto intermedio para conectarse a:
     - RDS (SQL Server)
     - Controlador de Dominio Windows en EC2

---

## 🔐 Resumen del Grupo de Seguridad (SG-Bastion)

| Protocolo | Puerto | Origen     | Propósito                        |
|-----------|--------|------------|----------------------------------|
| TCP       | 3389   | 0.0.0.0/0  | Acceso RDP al Bastion Host       |

Una vez conectado, las conexiones RDP o SQL adicionales se realizan **internamente**, desde el Bastion hacia los objetivos en la subred privada.

---

## ✅ Resultado

- Se estableció una máquina de salto segura para administración interna
- Se confirmó el acceso a RDS y al Controlador de Dominio mediante RDP
- No fue necesario exponer directamente los recursos privados a Internet

Siguiente: [RDS (SQL Server) ➜](rds.md)
