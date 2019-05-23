---
title: 'Inicio rápido: Escalabilidad horizontal de un proceso en Azure SQL Data Warehouse (PowerShell) | Microsoft Docs'
description: Escale procesos en Azure SQL Data Warehouse en PowerShell. Escale horizontalmente un proceso para aumentar el rendimiento, o bien revierta la escalabilidad del proceso para ahorrar costos.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: bd137b71cab4a345afce835effd2ecb0c03df312
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "66167025"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Inicio rápido: Escalabilidad horizontal de un proceso en Azure SQL Data Warehouse en PowerShell

Escale procesos en Azure SQL Data Warehouse en PowerShell. [Escale horizontalmente un proceso](sql-data-warehouse-manage-compute-overview.md) para aumentar el rendimiento, o bien revierta la escalabilidad del proceso para ahorrar costos.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="before-you-begin"></a>Antes de empezar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En este inicio rápido se da por supuesto que ya tiene una instancia de SQL Data Warehouse que puede escalar. Si tiene que crear una, consulte [Guía de inicio rápido: Creación de una instancia de Azure SQL Data Warehouse en Azure Portal, y realización de consultas en ella](create-data-warehouse-portal.md) para crear un almacenamiento de datos denominado **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) y siga las instrucciones de la pantalla.

```powershell
Connect-AzAccount
```

Para ver qué suscripción está usando, ejecute [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Si necesita usar una suscripción diferente de la predeterminada, ejecute [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Buscar información del almacenamiento de datos

Busque el nombre de la base de datos, el nombre del servidor y el grupo de recursos para el almacenamiento de datos que tiene previsto pausar y reanudar.

Siga estos pasos para buscar información de ubicación para el almacenamiento de datos.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. En la página izquierda de Azure Portal, haga clic en **Almacenes de SQL Data Warehouse**.
3. Seleccione **mySampleDataWarehouse** en la página **Almacenes de SQL Data Warehouse**. Se abrirá el almacenamiento de datos.

    ![Nombre del servidor y grupo de recursos](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Anote el nombre del almacenamiento de datos que se usará como nombre de la base de datos. Recuerda que un almacenamiento de datos es un tipo de base de datos. Además, anote el nombre del servidor y el grupo de recursos. Los usará en los comandos para pausar y reanudar.
5. Si su servidor es foo.database.windows.net, use solo la primera parte como nombre de servidor en los cmdlets de PowerShell. En la imagen anterior, el nombre completo del servidor es newserver-20171113.database.windows.net. Se usará **newserver-20180430** como nombre del servidor en el cmdlet de PowerShell.

## <a name="scale-compute"></a>Escalado de proceso

En SQL Data Warehouse, puede aumentar o reducir los recursos de procesos mediante el ajuste de las unidades del almacenamiento de datos. En [Guía de inicio rápido: Creación de una instancia de Azure SQL Data Warehouse en Azure Portal, y realización de consultas en ella](create-data-warehouse-portal.md) creó **mySampleDataWarehouse** y lo inició con 400 DWU. En los siguientes pasos se ajustan las DWU para **mySampleDataWarehouse**.

Para cambiar las unidades de almacenamiento de datos, use el cmdlet de PowerShell [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase). En el ejemplo siguiente se establecen las unidades de almacenamiento de datos en DW300 para la base de datos **mySampleDataWarehouse**, que se hospeda en el grupo de recursos **myResourceGroup** en el servidor **mynewserver-20180430**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-data-warehouse-state"></a>Comprobar el estado del almacenamiento de datos

Para ver el estado actual del almacenamiento de datos, use el cmdlet [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) de PowerShell. De esta forma, se obtiene el estado de la base de datos **mySampleDataWarehouse** del grupo de recursos **myResourceGroup** y el servidor **mynewserver-20180430.database.windows.net**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

El resultado será similar a este:

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Puede comprobar el **Estado** de la base de datos en la salida. En este caso, puede ver esta base de datos está en línea.  Al ejecutar este comando, debería recibir uno de los siguientes valores de Estado: En línea, Pausando, Reanudando, Escalando o Pausado.

Para ver el estado del servicio, use el comando siguiente:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Pasos siguientes
Ya ha aprendido a escalar procesos para el almacenamiento de datos. Para más información sobre Azure SQL Data Warehouse, siga el tutorial para cargar los datos.

> [!div class="nextstepaction"]
>[Carga de datos en una instancia de SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
