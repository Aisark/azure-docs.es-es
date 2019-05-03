---
title: Ejemplos de script de Azure PowerShell para SQL Database | Microsoft Docs
description: Ejemplos de scripts de Azure PowerShell para ayudar a crear y administrar servidores, grupos elásticos, bases de datos y firewalls de Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 6be6021ef828202ad37a8af4eba942e6898963ca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259990"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Ejemplos de Azure PowerShell para Azure SQL Database

Azure SQL Database le permite configurar las bases de datos, instancias y grupos mediante Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de manera local, en este tutorial se requiere la versión 1.4.0 de AZ PowerShell o posterior. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzAccount` para crear una conexión con Azure.

## <a name="single-database-and-elastic-pools"></a>Grupos elásticos y base de datos única

En la tabla siguiente se incluyen vínculos a scripts de Azure PowerShell para Azure SQL Database.

| |  |
|---|---|
|**Creación y configuración de bases de datos únicas y grupos elásticos**||
| [Creación de una base de datos única y configuración de una regla de firewall de servidor de bases de datos](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell crea una base de datos de Azure SQL única y configura una regla de firewall en el nivel de servidor. |
| [Creación de grupos elásticos y traslado de bases de datos agrupadas](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell crea grupos elásticos de Azure SQL Database, traslada las bases de datos agrupadas y cambia los tamaños de proceso.|
|**Configuración de la replicación geográfica y de la conmutación por error**||
| [Configuración y conmutación por error de una base de datos única mediante la replicación geográfica activa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell configura la replicación geográfica activa para una base de datos de Azure SQL única y la conmuta por error a la réplica secundaria. |
| [Configuración y conmutación por error de una base de datos agrupada mediante la replicación geográfica activa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell configura la replicación geográfica activa para una base de datos de Azure SQL en un grupo elástico de SQL y la conmuta por error a la réplica secundaria. |
| [Configuración y conmutación por error de un grupo de conmutación por error para una sola base de datos](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell configura un grupo de conmutación por error para una instancia de servidor de Azure SQL Database, agrega una base de datos al grupo de conmutación por error y lo conmuta por error al servidor secundario. |
|**Escalado de una base de datos única y un grupo elástico**||
| [Escalado de una base de datos única](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell supervisa las métricas de rendimiento de una base de datos de Azure SQL Database, la escala a un tamaño de proceso superior y crea una regla de alerta en una de las métricas de rendimiento. |
| [Escalado de un grupo elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell supervisa las métricas de rendimiento de grupo elástico de Azure SQL Database, lo escala a un tamaño de proceso superior y crea una regla de alerta en una de las métricas de rendimiento.  |
| **Detección de amenazas y auditoría** |
| [Configuración de detección de amenazas y auditoría](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell configura las directivas de auditoría y detección de amenazas para una base de datos de Azure SQL. |
| **Restauración, copia e importación de una base de datos**||
| [Restauración de una base de datos](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell restaura una base de datos de Azure SQL desde una copia de seguridad con redundancia geográfica y restaura una base de datos de Azure SQL eliminada a la copia de seguridad más reciente. |
| [Copia de una base de datos en un nuevo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell crea una copia de una base de datos de Azure SQL existente en un nuevo servidor SQL de Azure. |
| [Importación de una base de datos desde un archivo bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell importa una base de datos a un servidor SQL de Azure desde un archivo bacpac. |
| **Sincronización de datos entre bases de datos**||
| [Sincronización de datos entre bases de datos SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell configura la sincronización de datos para realizar la sincronización entre varias bases de datos de Azure SQL. |
| [Sincronización de datos entre la base de datos SQL Database y SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell configura la sincronización de datos entre una base de datos de Azure SQL y una base de datos de SQL Server local. |
| [Actualización del esquema de sincronización de SQL Data Sync](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell agrega o quita elementos del esquema de sincronización de Data Sync. |
|||

Obtenga más información sobre la [API de la base de datos única de Azure PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instancia administrada

En la tabla siguiente se incluyen vínculos a ejemplos de scripts de Azure PowerShell para Managed Instance de Azure SQL Database.

| |  |
|---|---|
|**Creación y configuración de instancias administradas**||
| [Creación y administración de una Instancia administrada](scripts/sql-database-create-configure-managed-instance-powershell.md) | Este script de PowerShell muestra cómo crear y administrar una Instancia administrada mediante Azure PowerShell |
| [Creación y administración de una instancia administrada con una plantilla de Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell muestra cómo crear y administrar una instancia administrada mediante Azure PowerShell y una plantilla de Azure Resource Manager.|
| **Configuración del Cifrado de datos transparente (TDE)**||
| [Administración del Cifrado de datos transparente en una instancia administrada con la propia clave desde Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell configura el Cifrado de datos transparente (TDE) en el escenario Bring Your Own Key para Instancia administrada de SQL de Azure, con una clave de Azure Key Vault|
|||

Obtenga más información sobre la [API de Instancia administrada de Azure PowerShell](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

## <a name="additional-resources"></a>Recursos adicionales

Los ejemplos que se enumeran en esta página, utilizan los [cmdlets de Azure SQL Database](/powershell/module/az.sql/) para crear y administrar recursos de SQL Azure. Otros cmdlets para ejecutar consultas y realizar varias tareas de base de datos se encuentran en el módulo [sqlserver](/powershell/module/sqlserver/). Para más información, consulte el artículo sobre [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
