---
title: Comprobar el estado, configurar el registro y recibir alertas - Azure Logic Apps | Microsoft Docs
description: Supervisión de estado, registro de datos de diagnóstico y configuración de alertas para Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: f6ece10c43c2c4a6bea92d14a8bf6fbdb49fd318
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261365"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Supervisar el estado, configurar el registro de diagnósticos y activar alertas para Azure Logic Apps

Después de [crear y ejecutar una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md), puede comprobar su historial de ejecuciones, historial de desencadenadores, estado y rendimiento. Para la supervisión de eventos en tiempo real y una depuración más rica, configure el [registro de diagnósticos](#azure-diagnostics) de la aplicación lógica. De este modo, puede [buscar y ver eventos](#find-events), como eventos de desencadenador, eventos de ejecución y eventos de acción. También puede usar estos [datos de diagnóstico con otros servicios](#extend-diagnostic-data), como Azure Storage y Azure Event Hubs. 

Para recibir notificaciones sobre errores u otros posibles problemas, configure [alertas](#add-azure-alerts). Por ejemplo, puede crear una alerta que detecte "cuando se produzcan errores en más de cinco ejecuciones en una hora". También puede configurar la supervisión, el seguimiento y el registro mediante programación con la [configuración de eventos y las propiedades de Azure Diagnostics](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Visualización del historial de ejecuciones y desencadenadores de la aplicación lógica

1. Para buscar la aplicación lógica en [Azure Portal](https://portal.azure.com), en el menú principal de Azure, elija **Todos los servicios**. En el cuadro de búsqueda, busque "logic apps" y elija **Logic Apps**.

   ![Búsqueda de la aplicación lógica](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure Portal muestra todas las aplicaciones lógicas asociadas a la suscripción de Azure. 

2. Seleccione la aplicación lógica y luego elija **Información general**.

   Azure Portal muestra el historial de ejecuciones y desencadenadores de la aplicación lógica. Por ejemplo:

   ![Historial de ejecuciones e historial de desencadenadores de la aplicación lógica](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Historial de ejecuciones** muestra todas las ejecuciones de la aplicación lógica. 
   * **Historial de desencadenadores** muestra toda la actividad de los desencadenadores de la aplicación lógica.

   Para obtener descripciones de estado, vea [Diagnóstico de errores en las aplicaciones lógicas](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Si no encuentra los datos que espera, en la barra de herramientas, elija **Actualizar**.

3. Para ver los pasos de una ejecución concreta, en **Historial de ejecuciones**, seleccione esa ejecución. 

   La vista de supervisión muestra cada paso de esa ejecución. Por ejemplo:

   ![Acciones de una ejecución concreta](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Para obtener más detalles sobre la ejecución, elija **Detalles de ejecución**. Esta información resume los pasos, el estado, las entradas y las salidas de la ejecución. 

   ![Selección de "Detalles de ejecución"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Por ejemplo, puede obtener el **Id. de correlación** de la ejecución, que podría necesitar al usar la [API de REST para aplicaciones lógicas](https://docs.microsoft.com/rest/api/logic).

5. Para obtener detalles sobre un paso concreto, elija ese paso. Ahora puede revisar detalles como las entradas, las salidas y los errores acontecidos en ese paso. Por ejemplo:

   ![Detalles del paso](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Todos los eventos y los detalles de runtime se cifran en el servicio Logic Apps. Solo se descifran cuando un usuario solicita ver esos datos. Puede controlar el acceso a estos eventos con el [control de acceso basado en roles (RBAC) de Azure](../role-based-access-control/overview.md).

6. Para obtener detalles sobre un evento de desencadenador concreto, vuelva al panel **Información general**. En **Historial de desencadenadores**, seleccione el evento de desencadenador. Ahora puede revisar detalles como las entradas y salidas, por ejemplo:

   ![Detalles de salida de evento de desencadenador](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Activación del registro de diagnósticos de la aplicación lógica

Para una depuración más rica con detalles y eventos de runtime, se puede configurar el registro de diagnóstico con [registros de Azure Monitor](../log-analytics/log-analytics-overview.md). Azure Monitor es un servicio de Azure que supervisa los entornos local y en la nube para ayudarlo a mantener su disponibilidad y rendimiento. 

Antes de empezar, necesita un área de trabajo de Log Analytics. Aprenda a [crear un área de trabajo de Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. En [Azure Portal](https://portal.azure.com), busque y seleccione la aplicación lógica. 

2. En el menú de la hoja de la aplicación lógica, en **Supervisión**, elija **Diagnóstico** > **Configuración de diagnóstico**.

   ![Supervisión, Diagnóstico, Configuración de diagnóstico](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. En **Configuración de diagnóstico**, elija **Activado**.

   ![Activación de los registros de diagnósticos](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Ahora seleccione el área de trabajo de Log Analytics y la categoría de evento para el registro como se muestra:

   1. Seleccione **Enviar a Log Analytics**. 
   2. En **Log Analytics**, elija **Configurar**. 
   3. En **Áreas de trabajo de OMS**, seleccione el área de trabajo que va a usar para el registro.
      > [!NOTE]
      > Las áreas de trabajo de OMS ahora se conocen como áreas de trabajo de Log Analytics.
   4. En **Registro**, seleccione la categoría **WorkflowRuntime**.
   5. Elija el intervalo métrico.
   6. Cuando termine, seleccione **Guardar**.

   ![Selección del área de trabajo de Log Analytics y los datos para el registro](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Ahora puede buscar eventos y otros datos de los eventos de desencadenador, los eventos de ejecución y los eventos de acción.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Búsqueda de eventos y datos de la aplicación lógica

Para buscar y ver eventos de la aplicación lógica, como eventos de desencadenador, eventos de ejecución y eventos de acción, siga estos pasos.

1. En [Azure Portal](https://portal.azure.com), elija **Todos los servicios**. Busque "log analytics" y luego elija **Log Analytics** como se muestra aquí:

   ![Selección de "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. En **Log Analytics**, busque y seleccione el área de trabajo de Log Analytics. 

   ![Selección del área de trabajo de Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. En **Administración**, elija **Búsqueda de registros**.

   ![Elija "Búsqueda de registros"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. En el cuadro de búsqueda, especifique un campo que quiera buscar y pulse **Entrar**. Cuando empiece a escribir, verá las posibles coincidencias y operaciones que puede usar. 

   Por ejemplo, para buscar los diez principales eventos que se han producido, escriba y seleccione esta consulta de búsqueda: **search Category == "WorkflowRuntime" | limit 10**

   ![Especificación de cadena de búsqueda](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Obtenga más información sobre [cómo buscar datos en los registros de Azure Monitor](../log-analytics/log-analytics-log-searches.md).

5. En la página de resultados, en la barra de la izquierda, elija el marco temporal que quiere ver.
Para refinar la consulta con un filtro, elija **+Agregar**.

   ![Selección del marco temporal de los resultados de la consulta](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. En **Agregar filtros**, escriba el nombre del filtro para poder encontrar el que quiere. Seleccione el filtro y elija **+Agregar**.

   En este ejemplo se usa la palabra "status" para buscar eventos con errores en **AzureDiagnostics**.
   El filtro de **status_s** ya está seleccionado.

   ![Selección de filtro](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. En la barra de la izquierda, seleccione el valor de filtro que quiere usar y elija **Aplicar**.

   ![Selección del valor de filtro y de "Aplicar"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Ahora vuelva a la consulta que está creando. La consulta se ha actualizado con el filtro y el valor seleccionados. Los resultados anteriores también se han filtrado.

   ![Consulta con los resultados filtrados](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Para guardar la consulta para su uso futuro, elija **Guardar**. Aprenda [cómo guardar la consulta](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Extensión de cómo y dónde usa los datos de diagnóstico con otros servicios

Además de con registros de Azure Monitor, puede usar los datos de diagnóstico de la aplicación lógica con otros servicios de Azure, por ejemplo: 

* [Archivar registros de Diagnósticos de Azure en Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Transmitir registros de Diagnósticos de Azure a Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Luego puede obtener supervisión en tiempo real mediante la telemetría y los análisis de otros servicios, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) y [Power BI](../azure-monitor/platform/powerbi.md). Por ejemplo:

* [Transmitir datos de Event Hubs a Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizar datos que se están transmitiendo con Stream Analytics y crear un panel de análisis en tiempo real en Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Según las opciones que quiera configurar, primero asegúrese de [crear una cuenta de Azure Storage](../storage/common/storage-create-storage-account.md) o [crear un centro de eventos de Azure](../event-hubs/event-hubs-create.md). Luego seleccione las opciones para el envío de los datos de diagnóstico:

![Envío de los datos a una cuenta de Azure Storage o a un centro de eventos](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Los períodos de retención solo se aplican cuando se usa una cuenta de almacenamiento.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configuración de alertas de la aplicación lógica

Para supervisar métricas concretas o umbrales superados de la aplicación lógica, configure [alertas de Azure](../azure-monitor/platform/alerts-overview.md). Más información sobre [métricas de Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Para configurar alertas sin [registros de Azure Monitor](../log-analytics/log-analytics-overview.md), siga estos pasos. Para usar criterios y acciones de alerta más avanzados, [configure registros de Azure Monitor](#azure-diagnostics) también.

1. En el menú de la hoja de la aplicación lógica, en **Supervisión**, elija **Diagnóstico** > **Reglas de alerta** > **Agregar alerta** como se muestra aquí:

   ![Adición de una alerta de la aplicación lógica](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. En la hoja **Agregar una regla de alerta**, cree la alerta como se muestra:

   1. En **Recurso**, seleccione la aplicación lógica si aún no está seleccionada. 
   2. Proporcione un nombre y una descripción para la alerta.
   3. Seleccione una **Métrica** o evento cuyo seguimiento quiera realizar.
   4. Seleccione una **Condición**, especifique un **Umbral** para la métrica y seleccione el **Periodo** para la supervisión de esta métrica.
   5. Seleccione si se va a enviar correo para la alerta. 
   6. Especifique cualquier otra dirección de correo electrónico para enviar la alerta. 
   También puede especificar la dirección URL de un webhook al que quiera enviar la alerta.

   Por ejemplo, esta regla envía una alerta cuando hay errores en cinco o más ejecuciones en una hora:

   ![Creación de regla de alerta de métrica](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Para ejecutar una aplicación lógica desde una alerta, puede incluir el [desencadenador de solicitud](../connectors/connectors-native-reqres.md) en el flujo de trabajo, lo que permite realizar tareas como estos ejemplos:
> 
> * [Publicar en Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar un texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Agregar un mensaje a una cola](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Configuración de eventos y detalles de Azure Diagnostics

Cada evento de diagnóstico incluye detalles sobre la aplicación lógica y ese evento, por ejemplo, el estado, la hora de inicio, la hora de finalización, etc. Para configurar mediante programación la supervisión, el seguimiento y el registro, puede usar estos detalles con la [API REST para Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) y la [API REST para Azure Diagnostics](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

Por ejemplo, el evento `ActionCompleted` tiene las propiedades `clientTrackingId` y `trackedProperties` que puede usar para el seguimiento y la supervisión:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: si no se ha proporcionado, Azure genera de forma automática este identificador y correlaciona eventos en una ejecución de aplicación lógica, incluidos los flujos de trabajo anidados que se llamen desde la aplicación lógica. Puede especificar manualmente este identificador desde un desencadenador si pasa un encabezado `x-ms-client-tracking-id` con el valor de identificador personalizado en la solicitud de desencadenador. Puede usar un desencadenador de solicitud, un desencadenador HTTP o un desencadenador de webhook.

* `trackedProperties`: para realizar el seguimiento de las entradas o salidas de los datos de diagnóstico, se pueden agregar propiedades controladas a acciones en la definición de JSON de la aplicación lógica. Las propiedades controladas solo pueden realizar el seguimiento de entradas y salidas de acciones individuales, aunque puede usar las propiedades `correlation` de los eventos para crear correlaciones entre las acciones de una ejecución.

  Para realizar el seguimiento de una o más propiedades, agregue la sección `trackedProperties` y las propiedades que quiera a la definición de la acción. Por ejemplo, imagine que quiere realizar un seguimiento de datos como un "id. de pedido" en los datos de telemetría:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```
  Este es otro ejemplo en el que se usa la acción **Inicializar variable**. En el ejemplo se agregan propiedades controladas desde la entrada de la acción, donde la entrada es una matriz, no un registro.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Pasos siguientes

* [Automatización de la implementación de aplicaciones lógicas](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Escenarios B2B y comunicación con Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Supervisión de mensajes B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
