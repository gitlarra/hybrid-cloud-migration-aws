# 🪟 Windows Server + EBS

Esta sección detalla el despliegue de una instancia de Windows Server en una subred privada, configurada como un Controlador de Dominio (DC), junto con la asociación y configuración de un volumen adicional EBS para ampliar su almacenamiento.

> ⚠️ Esto se implementó en un entorno de laboratorio de AWS Academy. Debido a restricciones (por ejemplo, sin roles de IAM o automatización de snapshots), algunas funcionalidades reales fueron simuladas manualmente.

---

## 🧭 Contexto y Objetivos

La configuración original incluía un Controlador de Dominio local con escalabilidad limitada y sin solución de backups. El objetivo de este despliegue fue:

- Alojar un Windows Server actuando como DC en una **subred privada**
- Permitir acceso RDP **solo desde el Bastion Host**
- Asociar almacenamiento adicional (volumen EBS) para simular backups de datos de dominio o almacenamiento de perfiles de usuario

---

## 🖥️ Configuración del Windows Server

1. **Se lanzó una instancia EC2**:
   - Sistema operativo: Microsoft Windows Server 2019
   - Subred: Subred privada en `company-vpc`
   - Asignación automática de IP pública: Desactivada
   - Par de claves: `.pem`

2. **Grupo de Seguridad: SG-WindowsDC**
   - Puerto 3389 permitido solo desde `SG-Bastion`

3. **Acceso RDP**:
   - Se accedió primero conectando al Bastion Host
   - Desde allí se utilizó la IP privada interna para acceder vía RDP al Windows Server

4. **Rol de Active Directory**:
   - La instancia fue promovida a Controlador de Dominio
   - Se creó un dominio y se unió desde una máquina cliente en pruebas internas

---

## 💾 Asociación del Volumen EBS

1. **Se creó un nuevo volumen EBS**:
   - Tamaño: 100 GB
   - Tipo: SSD de propósito general (gp2)
   - Zona de disponibilidad: La misma que la instancia de Windows Server

2. **Se adjuntó el volumen** desde la consola de AWS:
   - Dispositivo: `/dev/sdf` (se convierte automáticamente en `D:` en Windows)

3. **Inicialización del volumen** en Windows:
   - Acceso mediante RDP desde el Bastion Host
   - Se abrió el administrador de discos
   - Se inicializó el nuevo disco
   - Se creó y formateó un nuevo volumen como unidad `D:`
   - Se etiquetó la unidad (por ejemplo, `DomainData`)

> 📌 Esta configuración simula tener un volumen de almacenamiento separado para perfiles de dominio, logs o copias de seguridad.

---

## ✅ Resultado

- La instancia de Windows Server se alojó de forma segura en una subred privada
- El acceso RDP solo es posible a través del Bastion Host
- El rol de Controlador de Dominio fue configurado y está operativo
- El volumen adicional EBS fue montado y es utilizable como almacenamiento persistente

Siguiente: [S3 (Backups) ➜](6-s3-es.md)