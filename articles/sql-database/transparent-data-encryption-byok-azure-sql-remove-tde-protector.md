---
title: 'Eliminación del protector de TDE: PowerShell'
description: Guía de procedimientos para responder a un protector TDE potencialmente comprometido para una base de datos o un almacenamiento de datos de Azure SQL mediante TDE con compatibilidad con Bring Your Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 811e3bc206b4d98106bdbb1ce2655cd69c8585a2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589256"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Eliminación de un protector de Cifrado de datos transparente (TDE) con PowerShell

## <a name="prerequisites"></a>Prerrequisitos

- Debe tener una suscripción de Azure y ser un administrador en esa suscripción.
- Es preciso tener instalado y en ejecución Azure PowerShell.
- En esta guía de procedimientos se asume que ya utiliza una clave de Azure Key Vault como protector TDE para una base de datos o una base de datos de almacenamiento de datos de Azure SQL. Para más información, consulte [Cifrado de datos transparente con compatibilidad con BYOK](transparent-data-encryption-byok-azure-sql.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Para obtener instrucciones sobre la instalación del módulo Az, consulte [Instalación de Azure PowerShell](/powershell/azure/install-az-ps). Para los cmdlets concretos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> El módulo de Azure Resource Manager (RM) para PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. El módulo de AzureRM continuará recibiendo correcciones de errores hasta diciembre de 2020 como mínimo.  Los argumentos para los comandos del módulo Az y los módulos AzureRm son esencialmente idénticos. Para obtener más información sobre la compatibilidad, vea [Presentación del nuevo módulo Az de Azure PowerShell](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para la instalación, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Información general

En esta guía de procedimientos se describe cómo responder a un protector TDE potencialmente comprometido para una base de datos de Azure SQL o Data Warehouse mediante TDE con claves administradas por el usuario en Azure Key Vault y compatibilidad con Bring Your Own Key (BYOK). Para más información sobre la compatibilidad con BYOK para TDE, consulte la [página de introducción](transparent-data-encryption-byok-azure-sql.md).

Los siguientes procedimientos deben realizarse únicamente en casos extremos o en entornos de prueba. Lea la guía de procedimientos detenidamente, ya que si se eliminan protectores TDE que se usan activamente en Azure Key Vault la **base de datos dejará de estar disponible**.

Si alguna vez se sospecha que una clave está comprometida, de modo que un servicio o usuario ha tenido acceso no autorizado a la clave, lo mejor es eliminarla.

Tenga en cuenta que una vez que el protector de TDE se elimina en Key Vault, en un plazo máximo de 10 minutos todas las bases de datos cifradas comenzarán a denegar todas las conexiones con el mensaje de error correspondiente y su estado cambiará a [Inaccesible](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

En los siguientes pasos se describe cómo comprobar las huellas digitales de Protector de TDE que los archivos de registro virtual (VLF) de una base de datos determinada siguen utilizando.
Encontrará la huella digital del protector de TDE actual de la base de datos y el identificador de base de datos al ejecutar lo siguiente:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

La siguiente consulta devuelve los VLF y las huellas digitales correspondientes del sistema de cifrado que se están utilizando. Cada una de las distintas huellas digitales hace referencia a otra clave en Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

El comando de PowerShell **Get-AzureRmSqlServerKeyVaultKey**  proporciona la huella digital del Protector de TDE usado en la consulta para que pueda ver qué claves conservar y qué claves eliminar en AKV. Solo las claves que ya no usa la base de datos se pueden eliminar de forma segura de Azure Key Vault.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

El comando de PowerShell **az sql server key show**  proporciona la huella digital del Protector de TDE usado en la consulta para que pueda ver qué claves conservar y qué claves eliminar en AKV. Solo las claves que ya no usa la base de datos se pueden eliminar de forma segura de Azure Key Vault.

* * *

Esta guía de procedimientos explica dos enfoques dependiendo del resultado deseado después de la respuesta al incidente:

- Para que las bases de datos de Azure SQL y Data Warehouse estén **accesibles**
- Para que las bases de datos de Azure SQL y Data Warehouse estén **inaccesibles**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para que los recursos cifrados estén accesibles

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Cree una [nueva clave en Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Asegúrese de que esta nueva clave se crea en un almacén de claves independiente desde el protector de TDE potencialmente comprometido, ya que el control de acceso se aprovisiona en un nivel de almacén.

2. Agregue la nueva clave al servidor mediante los cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) y [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) y actualícela como el nuevo protector de TDE del servidor.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Asegúrese de que el servidor y las réplicas se han actualizado al nuevo protector de TDE con el cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector).

   > [!NOTE]
   > El nuevo protector de TDE puede tardar unos minutos en propagarse a todas las bases de datos y a las bases de datos secundarias del servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Realice una [copia de seguridad de la nueva clave](/powershell/module/az.keyvault/backup-azkeyvaultkey) en Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Elimine la clave comprometida de Key Vault mediante el cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey).

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Para restaurar una clave en Key Vault en el futuro mediante el cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey):

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para ver la referencia de comandos, consulte el [almacén de claves de la CLI de Azure](/cli/azure/keyvault/key).

1. Cree una [nueva clave en Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Asegúrese de que esta nueva clave se crea en un almacén de claves independiente desde el protector de TDE potencialmente comprometido, ya que el control de acceso se aprovisiona en un nivel de almacén.

2. Agregue la nueva clave al servidor y actualícelo como el nuevo protector de TDE del servidor.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Asegúrese de que el servidor y las réplicas se hayan actualizado al nuevo protector de TDE.

   > [!NOTE]
   > El nuevo protector de TDE puede tardar unos minutos en propagarse a todas las bases de datos y a las bases de datos secundarias del servidor.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Realice una copia de seguridad de la nueva clave en Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Elimine la clave en peligro de Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Restaurar una clave para Key Vault en el futuro.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para hacer inaccesibles los recursos cifrados

1. Quite las bases de datos que se están cifrando por la clave potencialmente comprometida.

   La copia de seguridad de la base de datos y los archivos de registro se realiza automáticamente, por lo que se puede realizar una restauración a un momento dado de la base de datos en cualquier momento (siempre y cuando se proporcione la clave). Las bases de datos deben eliminarse antes de eliminar un protector TDE activo para evitar la pérdida potencial de datos de hasta 10 minutos de las transacciones más recientes.

2. Realice una copia de seguridad del material de la clave del protector de TDE en Key Vault.
3. Eliminación de la clave potencialmente comprometida de Key Vault

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a cambiar el protector de TDE de un servidor para cumplir con los requisitos de seguridad: [Eliminación de un protector de Cifrado de datos transparente (TDE) con PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Introducción sobre la compatibilidad de Bring Your Own Key para TDE: [Activar TDE con su propia clave de Key Vault mediante PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
