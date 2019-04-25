---
title: Migración de SQL Server a Instancia administrada de Azure SQL Database con Database Migration Service y PowerShell | Microsoft Docs
description: Aprenda a migrar de SQL Server local a Instancia administrada de Azure SQL Database mediante Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: ed0d65a0f00bd5ebc3227a249beec6bafd791347
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532548"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Migración de SQL Server local a Instancia administrada de Azure SQL Database mediante Azure PowerShell
En este artículo, migrará la base de datos **Adventureworks2012** restaurada en una instancia local de SQL Server 2005 o superior a una Instancia administrada de Azure SQL Database mediante Microsoft Azure PowerShell. Puede migrar bases de datos desde una instancia de SQL Server local a Instancia administrada de Azure SQL Database mediante el módulo `Az.DataMigration` en Microsoft Azure PowerShell.

En este artículo, aprenderá a:
> [!div class="checklist"]
> * Cree un grupo de recursos.
> * Crear una instancia de Azure Database Migration Service.
> * Crear un proyecto de migración en una instancia de Azure Database Migration Service.
> * Ejecutar la migración.

## <a name="prerequisites"></a>Requisitos previos
Para completar estos pasos, necesitará lo siguiente:

- [SQL Server 2016 o posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (cualquier edición).
- Para habilitar el protocolo TCP/IP, que se deshabilita de forma predeterminada con la instalación de SQL Server Express. Habilite el protocolo TCP/IP siguiendo el artículo [Habilitar o deshabilitar un protocolo de red de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Configurar [Firewall de Windows para el acceso al motor de base de datos](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Una instancia de Azure SQL Database. Puede crear una instancia de Azure SQL Database siguiendo la información del artículo sobre cómo [crear una instancia de Azure SQL Database en Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (versión 3.3 o posterior).
- Haber creado una red virtual mediante el modelo de implementación de Azure Resource Manager, que proporciona la conectividad de sitio a sitio de Azure Database Migration Service a sus servidores de origen locales mediante [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ha completado la evaluación de la migración de base de datos y esquema de forma local mediante Data Migration Assistant como se describe en el artículo [realizar una evaluación de migración de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Para descargar e instalar el módulo Az.DataMigration desde la Galería de PowerShell mediante [Install-Module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Asegurarse de que las credenciales usadas para conectarse a la instancia de SQL Server de origen tenga el permiso [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Asegurarse de que las credenciales usadas para conectarse a la instancia de Azure SQL DB de destino tenga el permiso CONTROL DATABASE en las bases de datos Azure SQL Database de destino.
- Una suscripción de Azure. Si no tiene una, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicio de sesión en la suscripción de Microsoft Azure
Utilice las instrucciones que aparecen en el artículo [Inicio de sesión con Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) para iniciar sesión en su suscripción de Azure mediante PowerShell.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Cree un grupo de recursos para poder crear una máquina virtual.

Crear un grupo de recursos mediante el [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la región *EastUS*.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Creación de una instancia de Azure Database Migration Service 
Puede crear una instancia nueva de Azure Database Migration Service mediante el cmdlet `New-AzDataMigrationService`. Este cmdlet espera los siguientes parámetros requeridos:
- *Nombre del grupo de recursos de Azure*. Puede usar [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando para crear el grupo de recursos de Azure como se mostró anteriormente y proporcionar su nombre como un parámetro.
- *Nombre del servicio*. Cadena que se corresponde con el nombre de servicio único para Azure Database Migration Service. 
- *Ubicación*. Especifica la ubicación del servicio. Especifique una ubicación de centro de datos de Azure, por ejemplo, Oeste de EE. UU. o Sudeste Asiático.
- *SKU*. Este parámetro corresponde al nombre de SKU de DMS. Los nombres de SKU admitidos actualmente son *Basic_1vCore*, *Basic_2vCores* y *GeneralPurpose_4vCores*.
- *Identificador de subred virtual*. Puede usar el cmdlet [New AzVirtualNetworkSubnetConfig](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) para crear una subred. 

En el ejemplo siguiente se crea un servicio de nombre *MyDMS* en el grupo de recursos *MyDMSResourceGroup* que se encuentra en la región *Este de EE. UU.* con una red virtual denominada *MyVNET* y una subred llamada *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Creación de un proyecto de migración
Cree un proyecto de migración después de crear una instancia de Azure Database Migration Service. Un proyecto de Azure Database Migration Service requiere información de conexión para las instancias de origen y de destino, así como una lista de bases de datos que desee migrar como parte del proyecto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Creación de un objeto de información de conexión de base de datos para las conexiones de origen y de destino
Puede crear un objeto de información de conexión de base de datos mediante el cmdlet `New-AzDmsConnInfo`. Este cmdlet espera los siguientes parámetros:
- *ServerType*. El tipo de conexión de base de datos solicitado, por ejemplo, SQL, Oracle o MySQL. Utilice SQL para SQL Server y Azure SQL.
- *DataSource*. Nombre o dirección IP de una instancia de SQL Server o Azure SQL Database.
- *AuthType*. El tipo de autenticación para la conexión, que puede ser SqlAuthentication o WindowsAuthentication.
- El parámetro *TrustServerCertificate* establece un valor que indica si el canal se cifra mientras se omite recorrer la cadena de certificados para validar la confianza. El valor puede ser "true" o "false".

En el ejemplo siguiente se crea un objeto Connection Info para el servidor SQL Server de origen denominado MySourceSQLServer con autenticación de SQL: 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

En el ejemplo siguiente se muestra la creación de un objeto Connection Info para un servidor de Instancia administrada de Azure SQL Database denominado "targetmanagedinstance.database.windows.net" con autenticación de SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Ofrecimiento de bases de datos para el proyecto de migración
Cree una lista de objetos `AzDataMigrationDatabaseInfo` que especifique las bases de datos como parte del proyecto de Azure Database Migration que puede proporcionarse como parámetro para la creación del proyecto. El Cmdlet `New-AzDataMigrationDatabaseInfo` puede usarse para crear AzDataMigrationDatabaseInfo. 

En el siguiente ejemplo se crea el proyecto `AzDataMigrationDatabaseInfo` en la base de datos **AdventureWorks** y se agrega a la lista que se va a proporcionar como parámetro para la creación del proyecto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Creación de un objeto del proyecto
Por último, puede crear el proyecto de Azure Database Migration denominado *MyDMSProject* ubicado en el *Este de EE. UU.* con `New-AzDataMigrationProject` y agregar las conexiones de origen y de destino creadas anteriormente y la lista de bases de datos que migrar.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Creación e inicio de una tarea de migración
Por último, cree e inicie la tarea de Azure Database Migration. La tarea de Azure Database Migration requiere información de credenciales de conexión de origen y destino, y una lista de las tablas de base de datos que se van a migrar, además de la información ya proporcionada con el proyecto creado como requisito previo. 

### <a name="create-credential-parameters-for-source-and-target"></a>Creación de parámetros de credenciales de origen y de destino
Las credenciales de seguridad de conexión pueden crearse como un objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

En el ejemplo siguiente se muestra la creación de objetos *PSCredential* para las conexiones de origen y destino proporcionando contraseñas como variables de cadena *$sourcePassword* y *$targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>Creación de un objeto FileShare para copias de seguridad
Ahora cree el objeto de recurso compartido de archivos que representa el recurso compartido local de red SMB que Azure Database Migration Service puede tardar el origen de las copias de seguridad de base de datos para usar el cmdlet New-AzDmsFileShare.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Creación del objeto de la base de datos seleccionada
El siguiente paso es seleccionar las bases de datos de origen y destino mediante el cmdlet New-AzDmsSelectedDB, tal como se muestra en el ejemplo siguiente:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>URI de SAS del contenedor de Azure Storage
Cree una variable que contenga el URI de SAS que proporciona a Azure Database Migration Service acceso al contenedor de cuentas de almacenamiento en el que el servicio carga los archivos de copia de seguridad.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>Selección de inicios de sesión
Cree la lista de inicios de sesión del agente que se va a migrar tal y como se muestra en el ejemplo siguiente:  Tenga en cuenta que, actualmente, DMS solo admite la migración de los inicios de sesión SQL. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>Selección de trabajos del agente
Cree la lista de trabajos del agente que se va a migrar tal y como se muestra en el ejemplo siguiente:

>[!NOTE]
>Actualmente DMS solo admite trabajos con los pasos de trabajo del subsistema T-SQL.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>Creación e inicio de una tarea de migración

Utilice el cmdlet `New-AzDataMigrationTask` para crear e iniciar una tarea de migración. Este cmdlet espera los siguientes parámetros:
- *TaskType*. Tipo de tarea de migración que se espera crear para SQL Server con el tipo de migración de Instancia administrada de Azure SQL Database *MigrateSqlServerSqlDbMi*. 
- *Nombre del grupo de recursos*. Nombre del grupo de recursos de Azure en el que se crea la tarea.
- *ServiceName*. Instancia de Azure Database Migration Service en la que crear la tarea.
- *ProjectName*. Nombre del proyecto de Azure Database Migration Service en el que se va a crear la tarea. 
- *TaskName*. Nombre de la tarea que se va a crear. 
- *SourceConnection*. AzDmsConnInfo objeto que representa la conexión de SQL Server de origen.
- *TargetConnection*. AzDmsConnInfo objeto que representa la conexión de la instancia administrada de Azure SQL Database de destino.
- *SourceCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para la conexión al servidor de origen.
- *TargetCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para la conexión al servidor de destino.
- *SelectedDatabase*. AzDataMigrationSelectedDB objeto que representa la asignación de base de datos de origen y de destino.
- *BackupFileShare*. Objeto FileShare que representa el recurso compartido de red local en el que Azure Database Migration Service puede realizar las copias de seguridad de la base de datos de origen.
- *BackupBlobSasUri*. URI de SAS que proporciona a Azure Database Migration Service acceso al contenedor de cuentas de almacenamiento en el que el servicio carga los archivos de copia de seguridad. Aprenda a obtener el identificador URI de SAS del contenedor de blobs.
- *SelectedLogins*. Lista de los inicios de sesión seleccionados para la migración.
- *SelectedAgentJobs*. Lista de los trabajos de agente seleccionados para la migración.

En el siguiente ejemplo se crea y se inicia una tarea de migración denominada myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>Supervisión de la migración
Puede supervisar la tarea de migración que se ejecuta mediante la consulta de la propiedad de estado de la tarea como se muestra en el siguiente ejemplo:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Eliminación de la instancia DMS
Una vez completada la migración, puede eliminar la instancia de Azure DMS:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>Pasos siguientes
- Revise las directrices de migración en la [Guía de migración de bases de datos](https://datamigration.microsoft.com/).
