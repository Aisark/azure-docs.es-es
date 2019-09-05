---
title: Visualización de registros de actividad de cambios de RBAC en recursos de Azure | Microsoft Docs
description: Vea registros de actividad de los cambios de control de acceso basado en rol (RBAC) en recursos de Azure de los últimos 90 días.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5758f480c9216cf71e47509682053b39f0b15bf
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172417"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Visualización de registros de actividad de cambios de RBAC en recursos de Azure

En ciertas ocasiones, necesitará obtener información acerca de los cambios de control acceso basado en rol (RBAC) en recursos de Azure, por ejemplo, para fines de auditoría y para solucionar problemas. Siempre que un usuario realiza cambios en las definiciones de roles o las asignaciones de roles de las suscripciones, los cambios se registran en el [Registro de actividad de Azure](../azure-monitor/platform/activity-logs-overview.md). Puede consultar los registros de actividad para ver todos los cambios de RBAC de los últimos 90 días.

## <a name="operations-that-are-logged"></a>Operaciones que se registran

Estas son las operaciones relacionadas con el RBAC que se registran en el Registro de actividad:

- Creación de asignaciones de roles
- Eliminación de asignaciones de roles
- Creación o actualización de definiciones de roles personalizadas
- Eliminación de definiciones de roles personalizadas

## <a name="azure-portal"></a>Portal de Azure

La manera más fácil de empezar a trabajar es ver los registros de actividad con Azure Portal. En la siguiente captura de pantalla se muestra un ejemplo de un registro de actividad que se ha filtrado para mostrar las operaciones de definición de roles y asignación de roles. También incluye un vínculo para descargar los registros como un archivo CSV.

![Captura de pantalla Registros de actividad con el portal](./media/change-history-report/activity-log-portal.png)

El registro de actividad del portal tiene varios filtros. Estos son los filtros relacionados con RBAC:

|Filtrar  |Valor  |
|---------|---------|
|Categoría de eventos     | <ul><li>Administrativo</li></ul>         |
|Operación     | <ul><li>Creación de asignaciones de roles</li> <li>Eliminación de asignaciones de roles</li> <li>Creación o actualización de definiciones de roles personalizadas</li> <li>Eliminación de definiciones de roles personalizadas</li></ul>      |


Para obtener más información acerca de los registros de actividad, consulte [Ver eventos en el registro de actividad](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para ver los registros de actividad con Azure PowerShell, use el comando [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Este comando muestra todos los cambios de asignación de roles de una suscripción durante los últimos siete días:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Este comando muestra todos los cambios de definición de roles de un grupo de recursos durante los últimos siete días:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Este comando indica todos los cambios de definición de roles y asignación de roles de una suscripción durante los últimos siete días y muestra los resultados en una lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>CLI de Azure

Para ver los registros de actividad con la CLI de Azure, use el comando [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Este comando muestra los registros de actividad de un grupo de recursos desde la hora de inicio:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Este comando muestra los registros de actividad del proveedor de recursos de autorización desde la hora de inicio:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Registros de Azure Monitor

[Registros de Azure Monitor](../log-analytics/log-analytics-overview.md) es otra herramienta que se puede usar para recopilar y analizar los cambios de RBAC de todos los recursos de Azure. Registros de Azure Monitor tiene las siguientes ventajas:

- Escriba consultas y lógica complejas
- Integre con alertas, Power BI y otras herramientas
- Guarde datos durante períodos de retención más largos
- Cree referencias cruzadas con otros registros, como el de seguridad, máquina virtual y el personalizado

Estos son los pasos básicos para empezar a trabajar:

1. [Crear un área de trabajo de Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Configurar la solución Activity Log Analytics](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) para el área de trabajo.

1. [Ver los registros de actividad](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Una forma rápida de ir a la página Información general de la solución Activity Log Analytics es hacer clic en la opción **Log Analytics**.

   ![Opción de registros de Azure Monitor en el portal](./media/change-history-report/azure-log-analytics-option.png)

1. También puede usar la página [Búsqueda de registros](../log-analytics/log-analytics-log-search.md) o el [portal Advanced Analytics](../azure-monitor/log-query/get-started-portal.md) para consultar y ver los registros. Para obtener más información sobre estas dos opciones, vea [Portales para la creación y edición de consultas de registros en Azure Log Analytics](../azure-monitor/log-query/portals.md).

Esta es una consulta que devuelve las nuevas asignaciones de roles organizadas por proveedor de recursos de destino:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Esta es una consulta que devuelve los cambios de asignación de roles mostrados en un gráfico:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Captura de pantalla Registros de actividad mediante el portal Advanced Analytics](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Pasos siguientes
* [Visualización de eventos en el registro de actividad](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Supervise la actividad de suscripción con Azure Activity Log](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
