---
title: Transmisión de registros de diagnóstico de Azure a un centro de eventos
description: Aprenda a transmitir registros de diagnóstico de Azure a un centro de eventos.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: b5299af375646e7759d0770139df2cd6d7ce105c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60237703"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Transmisión de registros de diagnóstico de Azure a un centro de eventos
Los **[registros de diagnóstico de Azure](diagnostic-logs-overview.md)** se pueden transmitir casi en tiempo real a cualquier aplicación mediante la opción "Exportar a Event Hubs" integrada en el Portal o habilitando el identificador de regla de autorización de Event Hubs en una configuración de diagnóstico por medio de los cmdlets de Azure PowerShell o la CLI de Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Qué se puede hacer con registros de diagnóstico y Event Hubs
Estas son solo algunas formas de usar la funcionalidad de streaming para registros de diagnóstico:

* **Transmisión de registros a sistemas de registro y telemetría de terceros**: puede transmitir todos los registros de diagnóstico a un centro de eventos único para canalizar datos de registro a una herramienta de SIEM o de análisis de registros de terceros.
* **Visualización del estado del servicio mediante streaming de datos de "ruta de acceso frecuente" a Power BI**: con Event Hubs, Stream Analytics y Power BI, es fácil transformar los datos de diagnóstico en información casi en tiempo real de los servicios de Azure. [En este artículo se ofrece una excelente introducción a la configuración Event Hubs, al procesamiento de datos con Stream Analytics y al uso de Power BI como salida](../../stream-analytics/stream-analytics-power-bi-dashboard.md). Estas son algunas recomendaciones para la configuración con los registros de diagnóstico:

  * Un centro de eventos para una categoría de registros de diagnóstico se crea automáticamente al seleccionar la opción en el portal o habilitarla mediante PowerShell, por lo que debería seleccionar el centro de eventos en el espacio de nombres con el nombre que empieza por **insights-**.
  * El siguiente código SQL es una consulta de Stream Analytics de ejemplo que puede utilizar para analizar todos los datos de registro en una tabla de Power BI:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Creación de una plataforma personalizada de registro y telemetría** : si ya tiene una plataforma de telemetría personalizada o está pensando en crear una, la gran escalabilidad en cuanto a la suscripción y la publicación de Event Hubs permite introducir registros de diagnóstico de manera flexible. [Consulte la guía de Dan Rosanova para usar Event Hubs en una plataforma de telemetría de escala global aquí](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Habilitación del streaming de registros de diagnóstico

Puede habilitar el streaming de registros de diagnóstico mediante programación, a través del portal o mediante la [API de REST de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). En cualquier caso, se crea una configuración de diagnóstico en la que se especifica un espacio de nombres de Event Hubs y las categorías de registro y métricas que se desea enviar al espacio de nombres. Se crea un centro de eventos en el espacio de nombres para cada categoría de registro que se habilita. Una **categoría de registro** de diagnóstico es un tipo de registro que un recurso puede recopilar.

> [!WARNING]
> La habilitación y streaming de registros de diagnóstico desde recursos de proceso (por ejemplo, máquinas virtuales o Service Fabric) [requiere ejecutar una serie de pasos distinta](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

El espacio de nombres de Event Hubs no tiene que estar necesariamente en la misma suscripción que el recurso que emite los registros, siempre que el usuario que configure el valor tenga el acceso RBAC adecuado a ambas suscripciones y estas formen parte del mismo inquilino de AAD.

> [!NOTE]
> Actualmente no se admite el envío de métricas de varias dimensiones a través de la configuración de diagnóstico. Las métricas con dimensiones se exportan como métricas unidimensionales planas agregadas a través de los valores de dimensión.
>
> *Por ejemplo*: la métrica "Mensajes entrantes" de una instancia de Event Hub se puede explorar y representar gráficamente por colas. Sin embargo, cuando se exporta a través de la configuración de diagnóstico, la métrica se representará con todos los mensajes entrantes de todas las colas de Event Hub.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Streaming de registros de diagnóstico mediante el portal

1. En el portal, desplácese a Azure Monitor y haga clic en **Configuración de diagnóstico**.

    ![Sección de supervisión de Azure Monitor](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-blade.png)

2. Si lo desea, filtre la lista por tipo de recurso o por grupo de recursos y, a continuación, haga clic en el recurso para el que desea establecer la configuración de diagnóstico.

3. Si no existe ninguna configuración en el recurso que ha seleccionado, se le pide que cree una. Haga clic en "Activar diagnóstico".

   ![Agregar configuración de diagnóstico: sin configuración actual](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-none.png)

   Si hay una configuración actual en el recurso, verá una lista de opciones ya configuradas en este recurso. Haga clic en "Agregar configuración de diagnóstico".

   ![Agregar configuración de diagnóstico: configuración actual](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-multiple.png)

3. Asigne un nombre a su configuración y active la casilla **Transmitir a un centro de eventos**; a continuación, seleccione un espacio de nombres de Event Hubs.

   ![Agregar configuración de diagnóstico: configuración actual](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-configure.png)

   En el espacio de nombres seleccionado será donde se cree el centro de eventos (si es la primera vez que transmite registros de diagnóstico) o a donde se transmitan (si ya hay recursos que estén transmitiendo esa categoría de registro a este espacio de nombres); la directiva define los permisos que tiene el mecanismo de streaming. En la actualidad, para realizar streaming a un centro de eventos, se necesitan permisos de administración, envío y escucha. Puede crear o modificar las directivas de acceso compartido del espacio de nombres de Event Hubs en la pestaña Configurar del portal para su espacio de nombres. Para actualizar uno de estas opciones de diagnóstico, el cliente tiene que tener el permiso ListKey en la regla de autorización de Event Hubs. Opcionalmente, también puede especificar un nombre de centro de eventos. Si especifica un nombre de centro de eventos, los registros se enrutan a ese centro de eventos en lugar de a un concentrador de eventos recién creado por cada categoría de registro.

4. Haga clic en **Save**(Guardar).

Transcurridos unos instantes, la nueva opción de configuración aparece en la lista de opciones para este recurso y los registros de diagnóstico se transmiten en ese centro de eventos en cuanto se generan nuevos datos de eventos.

### <a name="via-powershell-cmdlets"></a>Mediante cmdlets de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar el streaming mediante [cmdlets de Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), puede utilizar el cmdlet `Set-AzDiagnosticSetting` con estos parámetros:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

El identificador de la regla de autorización de Event Hubs es una cadena con este formato: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, por ejemplo, `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Actualmente no puede seleccionar un nombre de centro de eventos determinado con PowerShell.

### <a name="via-azure-cli"></a>Mediante la CLI de Azure

Para habilitar el streaming a través de la [CLI de Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), puede usar el comando [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Puede agregar categorías adicionales al registro de diagnóstico mediante la incorporación de diccionarios a la matriz JSON que se pasa como el parámetro `--logs`.

El argumento `--event-hub-rule` usa el mismo formato que el identificador de la regla de autorización de Event Hubs que se explicó para el cmdlet de PowerShell.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>¿Cómo se consumen los datos de registro procedentes de Event Hubs?

Estos son datos de salida de ejemplo de Event Hubs:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| records |Matriz de todos los eventos de registro de esta carga. |
| time |Hora a la que se produjo el error. |
| category |Categoría de registro para este evento. |
| resourceId |Id. de recurso del recurso que generó este evento. |
| operationName |Nombre de la operación. |
| level |Opcional. Indica el nivel de registro de eventos. |
| properties |Propiedades del evento. |

Puede ver una lista de todos los proveedores de recursos que admiten el streaming a Event Hubs [aquí](diagnostic-logs-overview.md).

## <a name="stream-data-from-compute-resources"></a>Transmisión de datos de Recursos de proceso

También puede transmitir los registros de diagnóstico de los recursos de Compute mediante el agente de Microsoft Azure Diagnostics. [Consulte este artículo](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md) para ver cómo configurarlo.

## <a name="next-steps"></a>Pasos siguientes

* [Streaming de registros de Azure Active Directory con Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Más información sobre los registros de Diagnósticos de Azure](diagnostic-logs-overview.md)
* [Introducción a Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

