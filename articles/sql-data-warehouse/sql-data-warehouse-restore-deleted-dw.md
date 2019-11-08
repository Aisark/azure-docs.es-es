---
title: Restauración de un almacén de datos eliminado
description: Guía de procedimientos para restaurar una instancia de Azure SQL Data Warehouse eliminada.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cb09b4808bd6d59d2f70e85d204ab8451d501cee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692616"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Restauración de una instancia de Azure SQL Data Warehouse eliminada

En este artículo, aprenderá a restaurar una instancia de Azure SQL Data Warehouse eliminada desde Azure Portal y desde PowerShell:

## <a name="before-you-begin"></a>Antes de empezar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Compruebe la capacidad DTU**. Cada instancia de SQL Data Warehouse está hospedada en un servidor SQL Server (por ejemplo, myserver.database.windows.net) que tiene una cuota de DTU predeterminada.  Compruebe que el servidor SQL Server tiene suficiente cuota de DTU restante para la base de datos en proceso de restauración. Para más información sobre cómo calcular la unidad DTU necesaria o solicitar más DTU, consulte cómo [solicitar un cambio en la cuota de DTU][Request a DTU quota change].

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Restauración de una base de datos de almacenamiento de datos eliminada mediante PowerShell

Para restaurar una instancia de SQL Data Warehouse eliminada, use el cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase]. Si también se ha eliminado el servidor lógico correspondiente, la base de datos de almacenamiento de datos no se podrá restaurar.

1. Antes de empezar, asegúrese de [instalar Azure PowerShell][Install Azure PowerShell].
2. Abra PowerShell.
3. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
4. Seleccione la suscripción que contiene la base de datos de almacenamiento de datos eliminada que se va a restaurar.
5. Obtenga la base de datos de almacenamiento de datos eliminada específica.
6. Restauración de la base de datos de almacenamiento de datos eliminada
    1. Para restaurar la instancia de SQL Data Warehouse eliminada en un servidor lógico diferente, asegúrese de especificar el nombre del otro servidor lógico.  Este servidor lógico también puede estar en un grupo de recursos y una región diferentes.
    1. Para realizar la restauración en otra suscripción, use el botón [Mover][Move] para mover el servidor lógico a otra suscripción.
1. Compruebe que la base de datos de almacenamiento de datos restaurada está en línea.
1. Una vez finalizada la restauración, puede configurar la base de datos de almacenamiento de datos recuperada siguiendo la explicación que se proporciona en el apartado [Configuración de la base de datos después de realizar la recuperación][Configure your database after recovery].

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Uso de Azure Portal para restaurar una base de datos eliminada

1. Inicie sesión en el [Azure Portal][Azure portal].
2. Vaya al servidor de SQL Server en el que estaba alojada la base de datos de almacenamiento de datos eliminada.
3. Seleccione el icono **Bases de datos eliminadas** en la tabla de contenido.

    ![Bases de datos eliminadas](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Seleccione la instancia de SQL Data Warehouse eliminada que desea restaurar.

    ![Seleccionar bases de datos eliminadas](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Especifique un nuevo **nombre de base de datos** y haga clic en **Aceptar**

    ![Especificar nombre de base de datos](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Pasos siguientes
- [Restauración de un almacenamiento de datos existente][Restore an existing data warehouse]
- [Restauración desde un almacenamiento de datos de copia de seguridad geográfica][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[support ticket]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket
[Move]:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
