---
title: Puntos de conexión de servicio de red virtual para Azure Key Vault | Microsoft Docs
description: Introducción a los puntos de conexión de servicio de red virtual para Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: barbkess
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: d003e851dcfa77b18a67450946a71e4a13ce495c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700969"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Puntos de conexión de servicio de red virtual para Azure Key Vault

Los puntos de conexión de servicio de red virtual para Azure Key Vault permiten restringir el acceso a una red virtual especificada. También permiten restringir el acceso a una lista de intervalos de direcciones IPv4 (protocolo de Internet, versión 4). A todos los usuarios que se conecten a su almacén de claves desde fuera de esos orígenes se les negará el acceso.

Hay una excepción importante a esta restricción. Si un usuario ha decidido permitir los servicios de Microsoft de confianza, se habilitan las conexiones de esos servicios a través del firewall. Por ejemplo, estos servicios incluyen Office 365 Exchange Online, Office 365 SharePoint Online, la instancia de proceso de Azure, Azure Resource Manager y Azure Backup. Tales usuarios deben presentar un token de Azure Active Directory válido y necesitan tener permisos (configurados como directivas de acceso) para realizar la operación solicitada. Para obtener más información, consulte [puntos de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Escenarios de uso

Puede configurar los [firewalls y redes virtuales de Key Vault](key-vault-network-security.md) para denegar el acceso al tráfico de todas las redes (incluido el tráfico de Internet) de forma predeterminada. Puede conceder acceso al tráfico desde redes virtuales específicas de Azure y rangos de direcciones IP públicas de Internet, lo que le permite generar un límite de red seguro para las aplicaciones.

> [!NOTE]
> Los firewall de Key Vault y las reglas de red virtual solo se aplican al [plan de datos](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) de Key Vault. Las operaciones del plano de control de Key Vault (como crear, eliminar, modificar operaciones, configurar directivas de acceso, y configurar firewall y reglas de red virtual) no se ven afectadas por los firewall ni las reglas de red virtual.

Estos son algunos ejemplos de cómo puede usar los puntos de conexión de servicio:

* Si utiliza Key Vault para almacenar claves de cifrado, secretos de aplicación o certificados y quiere bloquear el acceso al almacén de claves de Internet pública.
* Si quiere bloquear el acceso al almacén de claves para que solo la aplicación o una lista breve de los hosts designados puedan conectarse al almacén de claves.
* Tiene una aplicación que se ejecuta en su red virtual de Azure y esta red virtual está bloqueada para todo el tráfico entrante y saliente. Su aplicación aún necesita conectarse a Key Vault para capturar secretos o certificados, o usar claves criptográficas.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configuración de firewalls y redes virtuales de Key Vault

Estos son los pasos necesarios para configurar firewalls y redes virtuales. Estos pasos se aplican si usa PowerShell, la CLI de Azure o Azure Portal.

1. Habilite el [registro de Key Vault](key-vault-logging.md) para ver registros de acceso detallados. Esto ayuda a realizar diagnósticos cuando los firewall y las reglas de red virtual impiden el acceso a un almacén de claves. Este paso es opcional, pero muy recomendable.
2. Habilite los **puntos de conexión de servicio para el almacén de claves** de las redes y subredes virtuales de destino.
3. Establezca firewall y reglas de red virtual para un almacén de claves, y así limitar el acceso a ese almacén de claves desde redes virtuales, subredes y rangos de direcciones IPv4 específicos.
4. Si este almacén de claves debe ser accesible para los servicios de confianza de Microsoft, debe habilitar la opción para permitir que los **servicios de confianza de Azure** se conecten a Key Vault.

Para más información, vea [Configuración de firewalls y redes virtuales de Azure Key Vault](key-vault-network-security.md).

> [!IMPORTANT]
> Una vez que las reglas del firewall están en vigor, los usuarios solo pueden realizar operaciones del [plano de datos](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) de Key Vault cuando las solicitudes se originan desde redes virtuales o rangos de direcciones IPv4 permitidos. Esto también se aplica al acceso de Key Vault desde Azure Portal. Aunque los usuarios pueden ir a un almacén de claves desde Azure Portal, es posible que no pueda enumerar las claves, los secretos o los certificados si su equipo cliente no está en la lista de dispositivos permitidos. Esto también afecta al selector de Key Vault de otros servicios de Azure. Los usuarios podrían ver la lista de almacenes de claves, pero no enumerar las claves si las reglas del firewall limitan su equipo cliente.


> [!NOTE]
> Tenga en cuenta las siguientes limitaciones de configuración:
> * Se permite un máximo de 127 reglas de red virtual y 127 reglas de IPv4. 
> * Los rangos de dirección pequeños con tamaños de prefijos "/31" o "/32" no son compatibles. En su lugar, configure estos rangos utilizando reglas de direcciones IP individuales.
> * Las reglas de red IP solo se permiten para direcciones IP públicas. No se permiten intervalos de direcciones IP reservados para redes privadas (tal y como se define en RFC 1918) en las reglas IP. Las redes privadas incluyen direcciones que empiecen con **10.**, **172.16 31**, y **192.168.**. 
> * Solo se admiten direcciones IPV4 en este momento.

## <a name="trusted-services"></a>Servicios de confianza

Esta es una lista de servicios de confianza que tienen permiso para acceder a un almacén de claves si está habilitada la opción **Permitir servicios de confianza**.

|Servicio de confianza|Escenarios de uso|
| --- | --- |
|Servicio de implementación de Azure Virtual Machines|[Implementar certificados en máquinas virtuales desde el almacén de claves administrado por el cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Servicio de implementación de plantillas de Azure Resource Manager|[Pasar valores seguros durante la implementación](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Servicio de cifrado de volúmenes de Azure Disk Encryption|Permitir el acceso a BitLocker Key (máquina virtual Windows) o a la frase de contraseña de DM (máquina virtual Linux) y a la clave de cifrado durante la implementación de máquinas virtuales. Esto habilita [Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Azure Backup|Permitir la opción de copia de seguridad y restauración de claves y secretos pertinentes durante la copia de seguridad de máquinas virtuales de Azure mediante [Azure Backup](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online y SharePoint Online|Permitir el acceso a la clave de cliente para Azure Storage Service Encryption con la [clave de cliente](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Permitir el acceso a la clave de inquilino para [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Implementar un certificado de Azure Web App mediante Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/).|
|Azure SQL Database|[Cifrado de datos transparente con compatibilidad con Bring Your Own Key para Azure SQL Database y Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Storage Service Encryption mediante claves administradas por el cliente en Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Cifrado de datos en Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) con una clave administrada de cliente.|
|Azure Databricks|[Servicio de análisis rápido, sencillo y de colaboración basado en Apache Spark](../azure-databricks/what-is-azure-databricks.md)|



> [!NOTE]
> Debe configurar las directivas de acceso pertinentes de Key Vault para permitir que los servicios correspondientes obtengan acceso a Key Vault.

## <a name="next-steps"></a>Pasos siguientes

* [Protección de Key Vault](key-vault-secure-your-key-vault.md)
* [Configuración de firewalls y redes virtuales de Azure Key Vault](key-vault-network-security.md)
