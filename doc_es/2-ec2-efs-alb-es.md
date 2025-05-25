# 🖥️ EC2 + EFS + Load Balancer

Este documento detalla la configuración de las instancias EC2, el almacenamiento compartido mediante EFS y la configuración del Application Load Balancer (ALB). Estos componentes están diseñados para servir contenido web de forma escalable y redundante en múltiples zonas de disponibilidad.

> ⚠️ Este despliegue se realizó en un entorno de laboratorio de AWS Academy, el cual limita el uso de roles de IAM y la automatización avanzada. Las reglas de acceso público como `0.0.0.0/0` se utilizan únicamente con fines académicos y no se recomendarían en un entorno de producción.

---

## 🧭 Contexto y Objetivos

La infraestructura on-premises albergaba sitios web estáticos en servidores locales. El objetivo de esta migración fue:

- Desplegar servidores web redundantes en la nube
- Compartir un único directorio de contenido entre múltiples EC2
- Enrutar el tráfico a través de un balanceador de carga centralizado
- Asegurar que los usuarios puedan acceder al sitio desde cualquier lugar mediante un DNS público

---

## 🖥️ Servidores Web EC2

1. **Se lanzaron 2 instancias EC2** (Debian 11) en diferentes Zonas de Disponibilidad
2. Se instalaron los paquetes necesarios:
   - Apache2
   - NFS Common (para montar EFS)
3. Se creó un directorio para el contenido web:  
   `/var/www/html/efs`

---

## 🔗 Elastic File System (EFS)

- Se creó un nuevo sistema de archivos EFS dentro de la misma VPC
- Se creó un punto de montaje en cada Zona de Disponibilidad
- Se asoció el grupo de seguridad SG-EFS
- En cada EC2:
  - Se montó el volumen EFS en `/var/www/html/efs`
  - Se verificó que los cambios en archivos se reflejaban entre ambas instancias
  - Se configuró el montaje persistente mediante `/etc/fstab`

> 💡 El sistema EFS garantiza que ambas EC2 sirvan el mismo contenido y permite escalar horizontalmente si es necesario.

---

## ⚙️ Configuración de Apache

- Se modificó el archivo de configuración de Apache:
  - Se actualizó el `DocumentRoot` para apuntar a `/var/www/html/efs`
- Se reinició el servicio de Apache:
  ```
  sudo systemctl restart apache2
  ```

---

## ⚖️ Application Load Balancer (ALB)

- Se creó un **Application Load Balancer** en las subredes públicas
- Grupo de seguridad asignado: **SG-ELB**
- Se creó un **Target Group**:
  - Tipo: Instancia
  - Protocolo: HTTP
  - Puerto: 80
- Se registraron ambas instancias EC2
- Se configuró el listener:
  - HTTP (puerto 80) → Reenvío al Target Group

> 🌐 El ALB proporciona un único DNS público para acceder a ambos servidores y distribuye el tráfico automáticamente.

---

## ✅ Resultado

- Dos servidores EC2 están sirviendo contenido idéntico
- El almacenamiento está compartido y sincronizado mediante EFS
- El ALB gestiona el tráfico externo y lo distribuye de forma equilibrada
- La configuración soporta alta disponibilidad y escalado horizontal

Siguiente: [Bastion Host ➜](bastion-host.md)
