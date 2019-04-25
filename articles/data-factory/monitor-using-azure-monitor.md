---
title: Supervisión de factorías de datos mediante Azure Monitor | Microsoft Docs
description: Aprenda a utilizar Azure Monitor para supervisar las canalizaciones de factorías de datos mediante la habilitación de registros de diagnóstico con la información de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: e96e462709ab0c715c831bd10c628869d5c617fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319332"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Alerta y supervisión de factorías de datos mediante Azure Monitor
Las aplicaciones de nube son complejas y tienen muchas partes móviles. La supervisión proporciona datos para garantizar que la aplicación permanece en funcionamiento en un estado correcto. También ayuda a evitar posibles problemas o a solucionar los existentes. Además, puede usar datos de supervisión para obtener un conocimiento más profundo sobre su aplicación. Este conocimiento puede ayudarle a mejorar el rendimiento o mantenimiento de la aplicación, o a automatizar acciones que de lo contrario requerirían intervención manual.

Azure Monitor ofrece registros y métricas de infraestructuras a nivel básico para la mayoría de los servicios de Microsoft Azure. Para más información, consulte la [introducción a la supervisión](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Los registros de diagnóstico de Azure son registros emitidos por un recurso que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. Data Factory da como resultado registros de diagnóstico en Azure Monitor.

## <a name="persist-data-factory-data"></a>Conservación de los datos de Data Factory
Data Factory solo almacena los datos de ejecución de canalización durante 45 días. Si desea conservar los datos de ejecución de canalización durante más de 45 días, con Azure Monitor puede, además de enrutar los registros de diagnóstico para su análisis, conservarlos en una cuenta de almacenamiento para tener información de Data Factory durante el tiempo que elija.

## <a name="diagnostic-logs"></a>Registros de diagnóstico

* Guardarlos en una **cuenta de almacenamiento** para archivarlos o inspeccionarlos manualmente. Puede especificar el tiempo de retención (en días) mediante la configuración de diagnóstico.
* Transmitirlos a **Event Hubs** para la ingesta en un servicio de terceros o una solución de análisis personalizado como Power BI.
* Analícelos con **Log Analytics**.

Puede usar una cuenta de almacenamiento o un espacio de nombres de centro de eventos que no esté en la misma suscripción que el recurso que emite los registros. El usuario que configura los ajustes debe tener el control de acceso basado en rol (RBAC) adecuado a ambas suscripciones.

## <a name="set-up-diagnostic-logs"></a>Configuración de registros de diagnósticos

### <a name="diagnostic-settings"></a>Configuración de diagnóstico
Los registros de diagnóstico para recursos no de proceso se configuran mediante Configuración de diagnóstico. Configuración de diagnóstico para un control de recurso:

* Dónde se envían los registros de diagnóstico (cuenta de almacenamiento, Event Hubs o registros de Azure Monitor).
* Qué categorías de registro se envían.
* Cuánto tiempo se debe conservar cada categoría de registro en una cuenta de almacenamiento.
* Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.
* Si se establecen las directivas de retención pero almacenar registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, solo Event Hubs o Azure Monitor están seleccionadas las opciones de registros), las directivas de retención no tienen ningún efecto.
* Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Habilitación de los registros de diagnóstico mediante las API de REST

Creación o actualización de una configuración de diagnóstico en la API de REST de Azure Monitor

**Solicitud**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Encabezados**
* Reemplace `{api-version}` por `2016-09-01`.
* Reemplace `{resource-id}` por el identificador del recurso para el que desea editar la configuración de diagnóstico. Para más información, consulte el artículo sobre el [uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Establezca el encabezado `Content-Type` en `application/json`.
* Establezca el encabezado de autorización en un token web de JSON que se obtiene de Azure Active Directory. Par más información, consulte [Solicitudes de autenticación](../active-directory/develop/authentication-scenarios.md).

**Cuerpo**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Propiedad | Escriba | DESCRIPCIÓN |
| --- | --- | --- |
| storageAccountId |string | El identificador de recurso de la cuenta de almacenamiento en la que le gustaría enviar los registros de diagnóstico |
| serviceBusRuleId |string | El identificador de regla de Service Bus para el espacio de nombres de Service Bus donde desea que se creen las instancias de Event Hub creadas por los registros de diagnóstico de streaming. El identificador de regla tiene el formato: "{Identificador de recurso de Service Bus}/authorizationrules/{nombre de clave}".|
| workspaceId | Tipo complejo | Matriz de intervalos de agregación de métricas y sus directivas de retención. Actualmente, esta propiedad está vacía. |
|Métricas| Valores de parámetros de la ejecución de canalización que se pasan a la canalización invocada| Un objeto JSON que asigna nombres de parámetro a los valores de argumento |
| logs| Tipo complejo| Nombre de una categoría de registro de diagnóstico para un tipo de recurso. Para obtener la lista de categorías de registro de diagnóstico para un recurso, realice primero una operación de configuración de diagnóstico GET. |
| category| string| Matriz de las categorías de registro y sus directivas de retención |
| timeGrain | string | La granularidad de las métricas que se capturan en formato de duración ISO 8601. Debe ser PT1M (un minuto).|
| Enabled| Boolean | Especifica si la colección de esa categoría de métrica o registro está habilitada para este recurso.|
| retentionPolicy| Tipo complejo| Describe la directiva de retención para una categoría de métrica o registro. Se utiliza solamente para la opción de cuenta de almacenamiento.|
| days| Int| Número de días para retener las métricas o registros. Con el valor cero, se retienen los registros indefinidamente. Se utiliza solamente para la opción de cuenta de almacenamiento. |

**Respuesta**

200 OK


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Permite obtener información sobre la configuración de diagnóstico en la API de REST de Azure Monitor.

**Solicitud**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Encabezados**
* Reemplace `{api-version}` por `2016-09-01`.
* Reemplace `{resource-id}` por el identificador del recurso para el que desea editar la configuración de diagnóstico. Para más información, consulte el artículo sobre el uso de grupos de recursos para administrar los recursos de Azure.
* Establezca el encabezado `Content-Type` en `application/json`.
* Establezca el encabezado de autorización en un token web de JSON que se obtiene de Azure Active Directory. Para más información, consulte Solicitudes de autenticación.

**Respuesta**

200 OK

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[Consulte más información aquí](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Esquema de registros y eventos

### <a name="activity-run-logs-attributes"></a>Atributos de registros de ejecución de actividad

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propiedad | Escriba | DESCRIPCIÓN | Ejemplo |
| --- | --- | --- | --- |
| Nivel |string | Nivel de los registros de diagnóstico. El nivel 4 siempre es el caso de los registros de ejecución de actividad. | `4`  |
| correlationId |string | Identificador único para realizar el seguimiento de una solicitud determinada completa | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Hora del evento de intervalo de tiempo, formato UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| string| Identificador de la ejecución de la actividad | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| string| Identificador de la ejecución de canalización | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| string | Identificador de recurso asociado para el recurso de la factoría de datos | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoría de los registros de diagnóstico. Establezca esta propiedad en "ActivityRuns". | `ActivityRuns` |
|level| string | Nivel de los registros de diagnóstico. Establezca esta propiedad en "Informational". | `Informational` |
|operationName| string |Nombre de la actividad con estado. Si el estado es el latido de inicio, es `MyActivity -`. Si el estado es el latido final, es `MyActivity - Succeeded` con estado final | `MyActivity - Succeeded` |
|pipelineName| string | Nombre de la canalización | `MyPipeline` |
|activityName| string | Nombre de la actividad | `MyActivity` |
|start| string | Inicio de la ejecución de la actividad en el intervalo de tiempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Finaliza la ejecución de la actividad en el intervalo de tiempo, formato UTC. Si la actividad no ha finalizado todavía (registro de diagnóstico para una actividad de inicio), se establece un valor predeterminado de `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-logs-attributes"></a>Atributos de registros de ejecución de canalización

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propiedad | Escriba | DESCRIPCIÓN | Ejemplo |
| --- | --- | --- | --- |
| Nivel |string | Nivel de los registros de diagnóstico. El nivel 4 es el caso de los registros de ejecución de actividad. | `4`  |
| correlationId |string | Identificador único para realizar el seguimiento de una solicitud determinada completa | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Hora del evento de intervalo de tiempo, formato UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| string| Identificador de la ejecución de canalización | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| string | Identificador de recurso asociado para el recurso de la factoría de datos | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoría de los registros de diagnóstico. Establezca esta propiedad en "PipelineRuns". | `PipelineRuns` |
|level| string | Nivel de los registros de diagnóstico. Establezca esta propiedad en "Informational". | `Informational` |
|operationName| string |Nombre de la canalización con estado. "Pipeline - Succeeded" (Canalización - Correcto) con estado final cuando se ha completado la ejecución de canalización| `MyPipeline - Succeeded` |
|pipelineName| string | Nombre de la canalización | `MyPipeline` |
|start| string | Inicio de la ejecución de la actividad en el intervalo de tiempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Final de las ejecuciones de la actividad en el intervalo de tiempo, formato UTC. Si la actividad no ha finalizado todavía (registro de diagnóstico para una actividad de inicio), se establece un valor predeterminado de `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|status| string | Estado final de la ejecución de canalización (correcto o erróneo) | `Succeeded`|

### <a name="trigger-run-logs-attributes"></a>Atributos de registros de ejecución de desencadenador

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Propiedad | Escriba | DESCRIPCIÓN | Ejemplo |
| --- | --- | --- | --- |
| Nivel |string | Nivel de los registros de diagnóstico. Se establece en el nivel 4 para registros de ejecución de actividad. | `4`  |
| correlationId |string | Identificador único para realizar el seguimiento de una solicitud determinada completa | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Hora del evento de intervalo de tiempo, formato UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| string| Identificador de la ejecución del desencadenador | `08587023010602533858661257311` |
|resourceId| string | Identificador de recurso asociado para el recurso de la factoría de datos | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoría de los registros de diagnóstico. Establezca esta propiedad en "PipelineRuns". | `PipelineRuns` |
|level| string | Nivel de los registros de diagnóstico. Establezca esta propiedad en "Informational". | `Informational` |
|operationName| string |Nombre del desencadenador con estado final si activa correctamente. "MyTrigger - Succeeded" (MyTrigger - Correcto) si el latido se realizó correctamente.| `MyTrigger - Succeeded` |
|triggerName| string | Nombre del desencadenador | `MyTrigger` |
|triggerType| string | Tipo del desencadenador (desencadenador manual o desencadenador de programación) | `ScheduleTrigger` |
|triggerEvent| string | Evento del desencadenador | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| string | Inicio de la activación del desencadenador en el intervalo de tiempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| string | Estado final de si el desencadenador se ha activado correctamente (correcto o erróneo) | `Succeeded`|

## <a name="metrics"></a>Métricas

Azure Monitor permite utilizar telemetría para obtener información sobre el rendimiento y el estado de las cargas de trabajo en Azure. El tipo de telemetría de datos de Azure más importante son las métricas (también denominadas contadores de rendimiento) emitidas por la mayoría de los recursos de Azure. Azure Monitor proporciona varias maneras de configurar y usar estas métricas para supervisar y solucionar problemas.

ADFV2 emite las siguientes métricas:

| **Métrica**           | **Nombre de métrica para mostrar**         | **Unidad** | **Tipo de agregación** | **Descripción**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Las métricas de ejecuciones de canalización se realizaron correctamente | Número    | Total                | Total de ejecuciones de canalizaciones realizadas correctamente dentro de una ventana de minutos |
| PipelineFailedRuns   | Métricas de ejecuciones de canalización erróneas    | Número    | Total                | Total de ejecuciones de canalizaciones erróneas dentro de una ventana de minutos    |
| ActivitySucceededRuns | Métricas de ejecución de actividad realizadas correctamente | Número    | Total                | Total de ejecuciones de actividad realizadas correctamente dentro de una ventana de minutos  |
| ActivityFailedRuns   | Métricas de ejecuciones de actividad erróneas    | Número    | Total                | Total de ejecuciones de actividad erróneas dentro de una ventana de minutos     |
| TriggerSucceededRuns | Métricas de ejecuciones de desencadenador realizadas correctamente  | Número    | Total                | Total de ejecuciones de desencadenador realizadas correctamente dentro de una ventana de minutos   |
| TriggerFailedRuns    | Métricas de ejecuciones de desencadenador erróneas     | Número    | Total                | Total de ejecuciones de desencadenador erróneas dentro de una ventana de minutos      |

Para obtener acceso a las métricas, siga las instrucciones del artículo: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Supervisión de las métricas de Data Factory con Azure Monitor

Puede usar la integración de Azure Data Factory con Azure Monitor para enrutar datos a Azure Monitor. Esta integración resulta útil en los escenarios siguientes:

1.  Quiere escribir consultas complejas en un amplio conjunto de métricas que se publican mediante Data Factory en Azure Monitor. También puede crear alertas personalizadas sobre estas consultas a través de Azure Monitor.

2.  Quiere realizar la supervisión entre fábricas de datos. Puede enrutar datos desde varias fábricas de datos a una sola área de trabajo de Azure Monitor.

Si desea una demostración y una introducción de siete minutos de esta característica, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configuración de las opciones de diagnóstico y el área de trabajo

Habilite la configuración de diagnóstico de su fábrica de datos.

1.  Seleccione **Azure Monitor** -> **Configuración de diagnóstico** -> Seleccione la fábrica de datos -> Activar diagnóstico.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Proporcione la configuración de diagnóstico, incluida la configuración del área de trabajo.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instale Azure Data Factory Analytics desde Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Haga clic en **Crear** y seleccione el área de trabajo y su configuración.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Métricas de Data Factory

Con la instalación de **Azure Data Factory Analytics** se crea un conjunto de vistas predeterminado que habilita las métricas siguientes:

- Ejecuciones de ADF: 1) Ejecuciones de canalización por Data Factory

- Ejecuciones de ADF: 2) Ejecuciones de actividad por Data Factory

- Ejecuciones de ADF: 3) Ejecuciones de desencadenador por Data Factory

- Errores de ADF: 1) Principales errores de canalización por Data Factory

- Errores de ADF: 2) Principales errores de actividad por Data Factory

- Errores de ADF: 3) Principales errores de desencadenador por Data Factory

- Estadísticas de ADF: 1) Ejecuciones de actividad por tipo

- Estadísticas de ADF: 2) Ejecuciones de desencadenador por tipo

- Estadísticas de ADF: 3) Duración máxima de las ejecuciones de canalización

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Puede visualizar las métricas anteriores, examinar las consultas detrás de estas métricas, editar las consultas, crear alertas, etc.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Alertas

Inicie sesión en Azure Portal y haga clic en **Supervisión &gt; Alertas** para crear alertas.

![Alertas en el menú del portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Creación de alertas

1.  Haga clic en **+ Nueva regla de alertas** para crear una nueva alerta.

    ![Nueva alerta de reglas](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Defina **Alert condition** (Condición de la alerta).

    > [!NOTE]
    > Asegúrese de seleccionar **Todo** en **Filter by resource type** (Filtrar por tipo de recurso).

    ![Condición de alerta, pantalla 1 de 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Condición de alerta, pantalla 2 de 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Condición de alerta, pantalla 3 de 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Defina los **Detalles de alertas**.

    ![Detalles de alertas](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Defina el **Grupo de acciones**.

    ![Grupo de acciones, pantalla 1 de 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Grupo de acciones, pantalla 2 de 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Grupo de acciones, pantalla 3 de 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Grupo de acciones, pantalla 4 de 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Pasos siguientes

Consulte el artículo [Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md) para obtener información sobre la supervisión y administración de canalizaciones con código.
