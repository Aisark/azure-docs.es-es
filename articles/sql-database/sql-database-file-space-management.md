---
title: Administración de espacio de archivo en bases de datos únicas o agrupadas
description: En esta página se describe cómo administrar el espacio de archivo con bases de datos únicas o agrupadas de Azure SQL Database, y se proporcionan ejemplos de código para determinar si se debe reducir una base de datos única o agrupada, y cómo hacerlo.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: a8fe58313bce6e9a21b07aa095672ec35ce572d2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803050"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Administración del espacio de archivo para bases de datos agrupadas y únicas en Azure SQL Database

En este artículo se describen los diferentes tipos de espacio de almacenamiento para bases de datos agrupadas y únicas de Azure SQL Database y los pasos que se pueden realizar cuando el espacio de archivo asignado para bases de datos y grupos elásticos necesita administrarse explícitamente.

> [!NOTE]
> Este artículo no es válido para la opción de implementación de instancias administradas de Azure SQL Database.

## <a name="overview"></a>Información general

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de Azure Resource Manager para PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Los argumentos para los comandos del módulo Az y los módulos AzureRm son esencialmente idénticos.

Con bases de datos únicas o agrupadas de Azure SQL Database, hay patrones de carga de trabajo donde la asignación de archivos de datos subyacentes para las bases de datos puede llegar a ser mayor que la cantidad de páginas de datos que se usan. Esta condición puede darse cuando el espacio usado aumenta y posteriormente se eliminan los datos. El motivo es que el espacio de archivo asignado no se reclama automáticamente cuando se eliminan los datos.

Es posible que sea necesario supervisar el uso del espacio de archivo y reducir los archivos de datos en los escenarios siguientes:

- Permitir el crecimiento de datos en un grupo elástico si el espacio de archivo asignado para sus bases de datos alcanza el tamaño máximo del grupo.
- Permitir la reducción del tamaño máximo de una instancia única de base de datos o grupo elástico.
- Permitir cambiar una instancia única de base de datos o grupo elástico a un nivel de servicio o un nivel de rendimiento diferente con un tamaño máximo inferior.

### <a name="monitoring-file-space-usage"></a>Supervisión del uso del espacio de archivo

La mayoría de las métricas de espacio de almacenamiento que aparecen en Azure Portal y en las API siguientes solo miden el tamaño de las páginas de datos que se usan:

- API de métricas basadas en Azure Resource Manager, como [get-metrics](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric) de PowerShell
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Sin embargo, las siguientes API también miden el tamaño del espacio asignado para las bases de datos y los grupos elásticos:

- T-SQL:  [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Reducción de los archivos de datos

El servicio SQL Database no reduce automáticamente los archivos de datos para reclamar el espacio asignado sin usar debido a las posibles repercusiones en el rendimiento de la base de datos.  Sin embargo, los clientes pueden reducir los archivos de datos a través de un autoservicio en el momento que estimen oportuno siguiendo los pasos descritos en [Reclamación del espacio asignado sin usar](#reclaim-unused-allocated-space).

> [!NOTE]
> A diferencia de los archivos de datos, el servicio SQL Database reduce automáticamente los archivos de registro, ya que esa operación no afecta al rendimiento de la base de datos. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Descripción de los tipos de espacio de almacenamiento para una base de datos

Comprender las cantidades de espacio de almacenamiento siguientes es importante para administrar el espacio de archivo de una base de datos.

|Cantidad de base de datos|Definición|Comentarios|
|---|---|---|
|**Espacio de datos usado**|La cantidad de espacio usado para almacenar los datos de la base de datos en páginas de 8 KB.|Por lo general, el espacio usado aumenta (disminuciones) en las inserciones (eliminaciones). En algunos casos, el espacio usado no cambia en las inserciones o eliminaciones, según la cantidad y el patrón de datos implicados en la operación y las posibles fragmentaciones. Por ejemplo, al eliminar una fila de cada página de datos no disminuye necesariamente el espacio usado.|
|**Espacio de datos asignado**|La cantidad de espacio de archivo de formato disponible para almacenar datos de la base de datos.|La cantidad de espacio asignado crece automáticamente, pero nunca disminuye después de las eliminaciones. Este comportamiento garantiza que las futuras inserciones son más rápidas puesto que no es necesario volver a formatear el espacio.|
|**Espacio de datos asignado, pero no usado**|La diferencia entre la cantidad de espacio de datos asignado y el espacio de datos usado.|Esta cantidad representa la cantidad máxima de espacio libre que se puede reclamar mediante la reducción de archivos de datos de base de datos.|
|**Tamaño máximo de datos**|La cantidad máxima de espacio que se puede usar para almacenar datos de base de datos.|La cantidad de espacio de datos asignado no puede crecer por encima del tamaño máximo de datos.|
||||

En el siguiente diagrama se ilustra la relación entre los diferentes tipos de espacio de almacenamiento para una base de datos.

![tipos de espacio de almacenamiento y relaciones](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Consulta de la información de espacio de almacenamiento en una base de datos única

Las consultas siguientes pueden utilizarse para determinar las cantidades de espacio de almacenamiento para una base de datos única.  

### <a name="database-data-space-used"></a>Espacio de datos de la base de datos usado

Modifique la siguiente consulta para devolver la cantidad de espacio de datos de base de datos usado.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espacio de datos de base de datos asignado y espacio asignado sin usar

Use la siguiente consulta para devolver la cantidad de espacio de datos de base de datos asignado y la cantidad de espacio asignado sin usar.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Tamaño máximo de datos de base de datos

Modifique la siguiente consulta para devolver el tamaño máximo de datos de la base de datos.  Las unidades del resultado de la consulta están en bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Descripción de los tipos de espacio de almacenamiento para un grupo elástico

Comprender las cantidades de espacio de almacenamiento siguientes es importante para administrar el espacio de archivo de un grupo elástico.

|Cantidad de grupo elástico|Definición|Comentarios|
|---|---|---|
|**Espacio de datos usado**|La suma del espacio de datos utilizado por todas las bases de datos en el grupo elástico.||
|**Espacio de datos asignado**|La suma del espacio de datos asignado por todas las bases de datos en el grupo elástico.||
|**Espacio de datos asignado, pero no usado**|La diferencia entre la cantidad de espacio de datos asignado y el espacio de datos usado por todas las base de datos del grupo elástico.|Esta cantidad representa la cantidad máxima de espacio asignado para el grupo elástico que se puede reclamar mediante la reducción de archivos de datos de base de datos.|
|**Tamaño máximo de datos**|La cantidad máxima de espacio de datos que puede usar el grupo elástico para todas sus bases de datos.|El espacio asignado para el grupo elástico no puede exceder el tamaño máximo del grupo elástico.  Si se da esta condición, el espacio asignado que no se usa se puede reclamar mediante la reducción de archivos de datos de base de datos.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consulta de la información de espacio de almacenamiento en un grupo elástico

Las consultas siguientes pueden utilizarse para determinar las cantidades de espacio de almacenamiento para un grupo elástico.  

### <a name="elastic-pool-data-space-used"></a>Espacio usado de datos de grupo elástico

Modifique la siguiente consulta para devolver la cantidad de espacio usado de datos del grupo elástico.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espacio de datos de grupo elástico asignado y espacio asignado sin usar

Modifique el siguiente script de PowerShell para devolver una tabla que muestre el espacio asignado y el espacio asignado sin usar para cada base de datos de un grupo elástico. La tabla ordena las bases de datos desde las que tienen más hasta las que tienen menos cantidad de espacio asignado sin usar.  Las unidades de resultado de la consulta están en MB.  

Los resultados de la consulta para determinar el espacio asignado para cada base de datos del grupo se pueden agregar juntos para determinar el espacio total asignado para el grupo elástico. El espacio de grupo elástico asignado no puede exceder el tamaño máximo del grupo elástico.  

El script de PowerShell requiere el módulo SQL Server PowerShell, consulte el artículo de [descarga del módulo de PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) para la instalación.

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

La captura de pantalla siguiente es un ejemplo de la salida del script:

![ejemplo de espacio asignado al grupo elástico y espacio asignado sin usar](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Tamaño máximo de datos del grupo elástico

Modifique la siguiente consulta T-SQL para devolver el tamaño máximo de datos del grupo elástico.  Las unidades de resultado de la consulta están en MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Reclamación del espacio asignado sin usar

> [!NOTE]
> Este comando puede afectar al rendimiento de la base de datos mientras se está ejecutando y, si es posible, se debe ejecutar durante períodos de poco uso.

### <a name="dbcc-shrink"></a>Reducción de DBCC

Una vez que se han identificado las bases de datos para reclamar el espacio asignado sin usar, modifique el nombre de la base de datos en el siguiente comando para reducir los archivos de datos para cada base de datos.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Este comando puede afectar al rendimiento de la base de datos mientras se está ejecutando y, si es posible, se debe ejecutar durante períodos de poco uso.  

Para más información sobre este comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

### <a name="auto-shrink"></a>Reducción automática

Como alternativa, la reducción automática puede habilitarse para una base de datos.  La reducción automática reduce la complejidad de la administración de archivos y afecta menos al rendimiento de la base de datos que `SHRINKDATABASE` o `SHRINKFILE`.  La reducción automática puede resultar especialmente útil para administrar grupos elásticos con muchas bases de datos.  Sin embargo, la reducción automática es menos eficaz al reclamar espacio de archivo que `SHRINKDATABASE` y `SHRINKFILE`.
Para habilitar la reducción automática, modifique el nombre de la base de datos en el siguiente comando.


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Para más información sobre este comando, consulte las opciones de [DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current). 

### <a name="rebuild-indexes"></a>Recompilación de índices

Después de reducir los archivos de datos de la base de datos, los índices se pueden fragmentar y perder su eficacia para la optimización del rendimiento. Si se produce una degradación del rendimiento, considere la posibilidad de recompilar los índices de base de datos. Para más información sobre la fragmentación y la recompilación de índices, consulte [Reorganizar y volver a generar índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Pasos siguientes

- Para información sobre los tamaños máximos de base de datos, consulte:
  - [Límites del modelo de compra basado en núcleo virtual de Azure SQL Database para una base de datos única](sql-database-vcore-resource-limits-single-databases.md)
  - [Límites de recursos para bases de datos únicas que utilizan el modelo de compra basado en DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Límites del modelo de compra basado en núcleo virtual de Azure SQL Database para grupos elásticos](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Límites de recursos para grupos elásticos que utilizan el modelo de compra basado en DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para más información sobre el comando `SHRINKDATABASE`, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Para más información sobre la fragmentación y la recompilación de índices, consulte [Reorganizar y volver a generar índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
