---
title: Creación de una configuración de diagnóstico para recopilar registros y métricas en Azure | Microsoft Docs
description: Cree una configuración de diagnóstico para reenviar los registros de plataforma de Azure a los registros de Azure Monitor, a Azure Storage o a Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ec1842d534dcb1e9ddef149d3ae879677b29e715
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263027"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure
Los [registros de plataforma](resource-logs-overview.md) en Azure proporcionan información detallada de diagnóstico y auditoría para los recursos de Azure y la plataforma de Azure de la que dependen. En este artículo se proporcionan detalles sobre la creación y la configuración de diagnóstico para la recopilación de los registros de plataforma en distintos destinos.

Cada recurso de Azure requiere su propia configuración de diagnóstico. La configuración de diagnóstico define lo siguiente para ese recurso:

- Categorías de los datos de los registros y las métricas que se envían a los destinos definidos en la configuración. Las categorías disponibles varían para los distintos tipos de recursos.
- Uno o más destinos para enviar los registros. Los destinos actuales incluyen el área de trabajo de Log Analytics, Event Hubs y Azure Storage.
- Directiva de retención para los datos almacenados en Azure Storage.
 
Una sola configuración de diagnóstico puede definir uno de los destinos. Si desea enviar datos a más de un tipo de destino determinado (por ejemplo, dos áreas de trabajo de Log Analytics diferentes), cree varias configuraciones. Cada recurso puede tener hasta 5 configuraciones de diagnóstico.

> [!NOTE]
> El registro de actividad se puede reenviar a los mismos destinos que los otros registros de plataforma, pero no está configurado para la configuración de diagnóstico. Consulte [Introducción a los registros de plataforma en Azure](platform-logs-overview.md#destinations) para más detalles.

> [!NOTE]
> Las [métricas de plataforma](metrics-supported.md) se recopilan automáticamente en las [métricas de Azure Monitor](data-platform-metrics.md). Las configuraciones de diagnóstico se pueden usar para recopilar métricas para determinados servicios de Azure en los registros de Azure Monitor para analizarlas con otros datos de supervisión mediante [consultas de registro](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinations 
Los registros de plataforma se pueden enviar a los destinos de la tabla siguiente. La configuración de cada destino se realiza mediante el mismo proceso de creación de la configuración de diagnóstico que se describe en este artículo. Siga los vínculos de la tabla siguiente para más información sobre cómo enviar datos a ese destino.

| Destino | DESCRIPCIÓN |
|:---|:---|
| [Área de trabajo de Log Analytics](resource-logs-collect-workspace.md) | La recopilación de registros de recurso en un área de trabajo de Log Analytics permite el análisis con otros datos de supervisión recopilados en por Azure Monitor mediante consultas de registro eficaces y también el aprovechamiento de otras características de Azure Monitor, como las alertas y las visualizaciones. |
| [Event Hubs](resource-logs-stream-event-hubs.md) | El envío de registros a Event Hubs permite transmitir datos a sistemas externos, como SIEM de terceros y otras soluciones de análisis de registros. |
| [Cuenta de Almacenamiento de Azure](resource-logs-collect-storage.md) | El archivado de registros en una cuenta de almacenamiento de Azure es útil para la auditoría, el análisis estático o la copia de seguridad. |




## <a name="create-diagnostic-settings-in-azure-portal"></a>Creación de configuraciones de diagnóstico en Azure Portal
Puede realizar configuraciones de diagnóstico en Azure Portal desde el menú de Azure Monitor o desde el menú del recurso.

1. En el menú de Azure Monitor de Azure Portal, haga clic en **Configuración de diagnóstico** de **Configuración** y después en el recurso.

    ![Configuración de diagnóstico](media/diagnostic-settings/menu-monitor.png)

    O bien, en el menú del recurso en Azure Portal, haga clic en **Configuración de diagnóstico** de **Supervisar**.

    ![Configuración de diagnóstico](media/diagnostic-settings/menu-resource.png)

2. Si no existe ninguna configuración en el recurso que ha seleccionado, se le pide que cree una. Haga clic en **Activar diagnóstico**.

   ![Agregar configuración de diagnóstico: sin configuración actual](media/diagnostic-settings/add-setting.png)

   Si hay una configuración actual en el recurso, verá una lista de opciones ya configuradas. Puede hacer clic en **Agregar configuración de diagnóstico** para agregar una nueva configuración o **Editar configuración** para modificar una existente. Cada configuración no puede tener más de uno de los tipos de destino.

   ![Agregar configuración de diagnóstico: configuración actual](media/diagnostic-settings/edit-setting.png)

3. Asigne un nombre a la configuración, si aún no lo tiene.
4. Marque la casilla de cada destino para enviar los registros. Haga clic en **Configurar** para especificar la configuración como se describe en la tabla siguiente.

    | Configuración | DESCRIPCIÓN |
    |:---|:---|
    | Área de trabajo de Log Analytics | Nombre del área de trabajo. |
    | Cuenta de almacenamiento | Nombre de la cuenta de almacenamiento. |
    | Espacio de nombres del centro de eventos | Espacio de nombres donde se crea el centro de eventos (si es la primera vez que transmite registros de diagnóstico) o a donde se transmita (si ya hay recursos que estén transmitiendo esa categoría de registro a este espacio de nombres).
    | Nombre del centro de eventos | Opcionalmente, especifique un nombre de centro de eventos para enviar todos los datos de la configuración. Si no especifica un nombre, se crea un centro de eventos para cada categoría de registro. Si va a enviar varias categorías, puede especificar un nombre para limitar el número de centros de eventos creados. Consulte [Cuotas y límites de Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) para más detalles. |
    | Nombre de la directiva del centro de eventos | Define los permisos que tiene el mecanismo de transmisión. |

    ![Agregar configuración de diagnóstico: configuración actual](media/diagnostic-settings/setting-details.png)

5. Marque la casilla de cada categoría de datos para su envío a los destinos especificados. Si seleccionó la opción **Archivar en una cuenta de almacenamiento**, también deberá especificar el [período de retención](resource-logs-collect-storage.md#data-retention).



> [!NOTE]
> Actualmente no se admite el envío de métricas de varias dimensiones a través de la configuración de diagnóstico. Las métricas con dimensiones se exportan como métricas unidimensionales planas agregadas a través de los valores de dimensión.
>
> *Por ejemplo*: la métrica "Mensajes entrantes" de una instancia de Event Hub se puede explorar y representar gráficamente por colas. Sin embargo, cuando se exporta a través de la configuración de diagnóstico, la métrica se representará con todos los mensajes entrantes de todas las colas de Event Hub.

4. Haga clic en **Save**(Guardar).

Transcurridos unos instantes, la nueva configuración aparece en la lista de configuraciones para este recurso y los registros se transmiten a los destinos especificados en cuanto se generan nuevos datos de eventos. Tenga en cuenta que pueden pasar hasta quince minutos desde que se emite un evento hasta que [aparece en el área de trabajo de Log Analytics](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Creación de una configuración de diagnóstico mediante PowerShell
Use el cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/en-us/powershell/module/az.monitor/set-azdiagnosticsetting) para crear una configuración de diagnóstico con [Azure PowerShell](powershell-quickstart-samples.md). Consulte la documentación de este cmdlet para las descripciones de sus parámetros.

A continuación se muestra un cmdlet de PowerShell de ejemplo para crear una configuración de diagnóstico con los tres destinos.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Creación de configuraciones de diagnóstico con la CLI de Azure
Use el comando [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) para crear una configuración de diagnóstico con la [CLI de Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Consulte la documentación de este comando para las descripciones de sus parámetros.

A continuación se muestra un comando de la CLI de ejemplo para crear una configuración de diagnóstico con los tres destinos.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Creación de configuraciones de diagnóstico mediante API REST
Consulte [Configuración de diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) para crear o actualizar configuraciones de diagnóstico mediante la [API REST de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Creación de configuraciones de diagnóstico mediante una plantilla de Resource Manager
Consulte [Habilitación automática de las configuraciones de diagnóstico durante la creación del recurso mediante una plantilla de Resource Manager](diagnostic-settings-template.md) para crear o actualizar configuraciones de diagnóstico con una plantilla de Resource Manager.

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los registros de plataforma de Azure](resource-logs-overview.md)