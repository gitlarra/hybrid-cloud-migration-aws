# 🗃️ Amazon S3 (Copias de seguridad)

Esta sección describe cómo se utilizó Amazon S3 para almacenar copias de seguridad manuales de la base de datos SQL Server y otros datos del entorno. Proporciona una capa básica de almacenamiento en la nube accesible desde recursos internos sin exponerlos a Internet.

> ⚠️ Todas las acciones se realizaron dentro de un entorno de laboratorio de AWS Academy, el cual limita la automatización, las políticas de ciclo de vida y los roles de IAM. Las copias de seguridad se realizaron manualmente y el acceso se configuró para funcionar de forma segura desde subredes privadas usando un endpoint de S3 Gateway.

---

## 🧭 Contexto y Objetivos

Debido a las restricciones del laboratorio, no fue posible implementar una solución completa de copias de seguridad automatizadas. En su lugar, el objetivo fue:

- Crear una ubicación privada y segura para almacenar archivos de backup `.sql`
- Asegurar que servicios internos como RDS o Windows Server pudieran subir backups
- Evitar cualquier acceso público o exposición a Internet
- Simular una estrategia escalable de backup en una arquitectura híbrida

---

## 🗂️ Configuración del Bucket

1. **Se creó un nuevo bucket de S3** en la misma región que la VPC
   - Nombre: `company-backups` (o similar)
   - Se bloqueó todo acceso público (por defecto)
   - Versionado: Desactivado (no requerido para backups manuales)
   - Cifrado: No aplicado (no disponible en el laboratorio)

2. **Se crearon carpetas dentro del bucket** para organización:
   - `/sql_backups/`
   - `/manual_exports/`

3. **Convención de nombres para los archivos de backup**:
   - `backup_bdcompany_DD_MM_YYYY.sql`
   - Ejemplo: `backup_bdcompany_10_04_2024.sql`

---

## 🔐 Acceso Privado mediante S3 Gateway (como se haría)

Para permitir el acceso desde subredes privadas (por ejemplo, Windows Server o Bastion), se utilizó un **S3 Gateway Endpoint**:

1. **Se creó un endpoint de Gateway** en la VPC
   - Servicio: `com.amazonaws.<región>.s3`
   - Tablas de enrutamiento: Asociadas a todas las subredes privadas

2. **Resultado**: Windows Server en la subred privada pudo subir backups directamente a S3 **sin necesidad de acceso a Internet**.

> 🛡️ Esta es una forma segura y rentable de permitir que recursos internos interactúen con S3.

---

## ✅ Resultado

- Las copias de seguridad se subieron correctamente desde el Bastion y Windows Server
- Los archivos se almacenaron de forma privada en un bucket S3 seguro
- El acceso público fue completamente deshabilitado
- Se demostró cómo un entorno híbrido puede aprovechar S3 sin NAT ni gateways a Internet

Proyecto completado 🎉
