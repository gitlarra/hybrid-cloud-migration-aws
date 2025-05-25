# 🧱 VPC y Grupos de Seguridad

Este documento describe la configuración inicial de la Virtual Private Cloud (VPC) de AWS y de los grupos de seguridad relacionados, utilizados para aislar, proteger y permitir la comunicación dentro de la infraestructura híbrida. Esta es la base sobre la que se construyeron todos los servicios en la nube.

> ⚠️ Esta configuración se realizó en un entorno de laboratorio de AWS Academy, el cual tiene algunas limitaciones como la ausencia de roles de IAM y direcciones IP públicas fijas. Estas restricciones afectan ciertas buenas prácticas, como el uso de 0.0.0.0/0 en las reglas de los grupos de seguridad.

---

## 🧭 Contexto y Objetivos

Antes de desplegar cualquier servicio en AWS, era esencial diseñar una red segura y escalable. El objetivo era dividir la infraestructura en **subredes públicas y privadas** distribuidas entre varias Zonas de Disponibilidad (AZs), garantizando redundancia, segmentación y control estricto del acceso.

Objetivos clave:
- Habilitar zonas aisladas para servicios públicos e internos
- Soportar RDS y Windows Server dentro de subredes privadas
- Asegurar el acceso solo mediante Bastion Host
- Preparar EFS y EC2 para el despliegue web

---

## 🌐 Configuración de la VPC

1. **Se creó una nueva VPC** mediante el asistente de VPC de AWS
   - Nombre: `company-vpc`
   - Bloque CIDR: `10.0.0.0/16` (permite un gran número de IPs)

2. **Se crearon 6 subredes** distribuidas en 3 Zonas de Disponibilidad:
   - 3 **Subredes públicas** (2 para servidores web EC2, 1 para Bastion Host)
   - 3 **Subredes privadas** (1 para RDS y Windows Server DC, 2 sin usar)

3. **Gateway NAT desactivado por defecto** por motivos de seguridad
   - Puede activarse temporalmente si el Windows Server necesita actualizarse

4. **Gateway S3 activado** para permitir que los recursos en subredes privadas accedan a S3 sin exponerse a Internet
   - No se usa en este caso por la falta de roles IAM

5. **Activación de IP pública automática para subredes públicas**
   - AWS requiere que esta opción esté habilitada a nivel de subred para que las instancias EC2 obtengan una IP pública (aunque se configure DHCP en su creación)

---

## 🔐 Configuración de Grupos de Seguridad

Los Grupos de Seguridad (SG) se asignan a servicios específicos y definen reglas de tráfico para cada componente. Todos fueron creados dentro del contexto de `company-vpc`.

> 🔒 Las reglas de salida se dejaron por defecto (todo el tráfico permitido)

### SG-ELB (Application Load Balancer)
| Protocolo | Puerto | Origen     | Descripción            |
|-----------|--------|------------|-------------------------|
| TCP       | 80     | 0.0.0.0/0  | Acceso HTTP público     |

### SG-WebServer (Servidores EC2 Web)
| Protocolo | Puerto | Origen       | Descripción                     |
|-----------|--------|--------------|---------------------------------|
| TCP       | 80     | SG-ELB       | Tráfico HTTP desde ALB          |
| TCP       | 22     | 0.0.0.0/0    | Acceso SSH para configuración   |
| TCP       | 2049   | SG-EFS       | NFS (montaje compartido EFS)    |

### SG-EFS (Elastic File System)
| Protocolo | Puerto | Origen       | Descripción                     |
|-----------|--------|--------------|---------------------------------|
| TCP       | 2049   | SG-WebServer | Permitir a EC2 montar EFS       |

### SG-Bastion (Host Bastion Windows)
| Protocolo | Puerto | Origen     | Descripción                     |
|-----------|--------|------------|---------------------------------|
| TCP       | 3389   | 0.0.0.0/0  | Acceso RDP desde cualquier lugar|

### SG-WindowsDC (Controlador de Dominio)
| Protocolo | Puerto | Origen     | Descripción                            |
|-----------|--------|------------|----------------------------------------|
| TCP       | 3389   | SG-Bastion | Acceso RDP solo a través del Bastion   |

### SG-RDS (Base de Datos SQL Server)
| Protocolo | Puerto | Origen     | Descripción                                    |
|-----------|--------|------------|------------------------------------------------|
| TCP       | 1433   | SG-Bastion | Acceso a SQL Server desde el Bastion Host      |

> 🧪 Todas las configuraciones de SG se validaron mediante pruebas de conexión para confirmar el aislamiento adecuado y la accesibilidad solo por rutas autorizadas.

---

## ✅ Resultado

- Subredes y rutas preparadas para los siguientes despliegues (EC2, EFS, RDS)
- Servicios públicos (web) aislados de los internos (BD, DC)
- Segmentación clara y principio de mínimo privilegio aplicado en todos los componentes

Siguiente: [EC2 + EFS + Load Balancer ➜](ec2-efs-alb.md)
