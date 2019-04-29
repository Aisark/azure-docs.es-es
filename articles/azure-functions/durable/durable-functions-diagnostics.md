---
title: 'Diagnóstico con Durable Functions: Azure'
description: Aprenda a diagnosticar problemas con la extensión Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 167f697d4928d88114a30739a1d39a576c87ac84
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126662"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnóstico con Durable Functions en Azure

Existen varias opciones para diagnosticar problemas con [Durable Functions](durable-functions-overview.md). Algunas de ellas son las mismas que para las funciones normales y otras son exclusivas de Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) es la manera recomendada para realizar el diagnóstico y la supervisión en Azure Functions. Lo mismo se aplica a Durable Functions. Para introducción al aprovechamiento de Application Insights en las aplicaciones de función, consulte [Supervisión de Azure Functions](../functions-monitoring.md).

La extensión Azure Durable Functions también emite *eventos de seguimiento* que permiten realizar el seguimiento de la ejecución de un extremo de una orquestación al otro. Estos se pueden encontrar y consultar mediante la herramienta [Application Insights Analytics](../../azure-monitor/app/analytics.md) de Azure Portal.

### <a name="tracking-data"></a>Datos de seguimiento

Cada evento de ciclo de vida de una instancia de orquestación provoca uno de seguimiento que se escribirá en la colección **Seguimientos** de Application Insights. Este evento contiene una carga **customDimensions** con varios campos.  Los nombres de campo llevan `prop__` antepuesto.

* **hubName**: nombre de la central de tareas donde se ejecutan las orquestaciones.
* **appName**: Nombre de la aplicación de función. Es útil cuando tiene varias aplicaciones de función que compartan la misma instancia de Application Insights.
* **slotName**: la [ranura de implementación](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) donde se ejecuta la aplicación de función actual. Es útil cuando se aprovechan las ranuras de implementación para modificar las orquestaciones.
* **functionName**: nombre de la función de actividad o de orquestador.
* **functionType**: tipo de función, como **Orquestador** o **Actividad**.
* **instanceId**: identificador único de la instancia de orquestación.
* **state**: estado de ejecución del ciclo de vida de la instancia. Los valores válidos son:
  * **Scheduled**: la función estaba programada para ejecutarse, pero no se ha iniciado todavía la ejecución.
  * **Started**: la función ha empezado a ejecutarse, pero todavía no está en espera ni completada.
  * **Awaited**: el orquestador ha programado trabajo y está esperando a que finalice.
  * **Listening**: el orquestador está escuchando una notificación de eventos externa.
  * **Completed**: la función se ha completado correctamente.
  * **Failed**: se ha producido un error en la función.
* **reason**: información adicional asociada al evento de seguimiento. Por ejemplo, si una instancia está esperando una notificación de eventos externa, este campo indica el nombre del evento al que espera. Si se produce un error en una función, contendrá los detalles del error.
* **isReplay**: valor booleano que indica si el evento de seguimiento va a volver a reproducir la ejecución.
* **extensionVersion**: versión de la extensión Durable Task. Se trata de datos especialmente importantes al informar de posibles errores en la extensión. Las instancias de ejecución prolongada pueden notificar varias versiones si se produce una actualización mientras se están ejecutando.
* **sequenceNumber**: número de secuencia de ejecución de un evento. En combinación con timestamp, ayuda a ordenar los eventos por hora de ejecución. *Tenga en cuenta que este número se restablecerá a cero si se reinicia el host mientras se ejecuta la instancia, por lo que es importante que siempre se ordene por timestamp en primer lugar y, a continuación, por sequenceNumber.*

El nivel de detalle de los datos de seguimiento que se emiten para Application Insights puede configurarse en las secciones `logger` (Functions 1.x) o `logging` (Functions 2.x) del archivo `host.json`.

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

De forma predeterminada, se emiten todos los eventos de seguimiento de no reproducción. Se puede reducir el volumen de datos; para ello, configure `Host.Triggers.DurableTask` en `"Warning"` o `"Error"`, en cuyo caso los eventos de seguimiento solo se emitirá para las situaciones excepcionales.

Para habilitar la emisión de los eventos de reproducción de orquestación detallada, `LogReplayEvents` se puede establecer en `true` en el archivo `host.json` en `durableTask`, tal como se muestra:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> De forma predeterminada, el sistema en tiempo de ejecución de Azure Functions muestrea la telemetría de Application Insights para evitar que se emitan datos con demasiada frecuencia. Esto puede provocar que se pierda información de seguimiento cuando se producen muchos eventos de ciclo de vida en un breve período de tiempo. En el artículo [Supervisión de Azure Functions](../functions-monitoring.md#configure-sampling) se explica cómo configurar este comportamiento.

### <a name="single-instance-query"></a>Consultas en una sola instancia

La consulta siguiente muestra datos históricos de seguimiento de una única instancia de la función de orquestación [Hello Sequence](durable-functions-sequence.md). Se escribe con el [lenguaje de consulta de Application Insights](https://aka.ms/LogAnalyticsLanguageReference). Filtra la ejecución de la reproducción de manera que se muestra solo la ruta de ejecución *lógica*. Los eventos se pueden ordenar por `timestamp` y `sequenceNumber`, tal y como se muestra en la siguiente consulta:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

El resultado es una lista de eventos de seguimiento que muestran la ruta de ejecución de la orquestación, funciones de actividad incluidas, ordenadas por hora de ejecución en orden ascendente.

![Consultas en Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Consultas de resumen de instancias

La consulta siguiente muestra el estado de todas las instancias de orquestación que se ejecutaron en un intervalo de tiempo especificado.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

El resultado es una lista de identificadores de instancia y su estado actual en tiempo de ejecución.

![Consultas en Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registro

Es importante tener en cuenta el comportamiento de reproducción del orquestador al escribir registros directamente desde una función de orquestador. Por ejemplo, considere la función de orquestador siguiente:

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Los datos de registro resultantes se parecerán a lo siguiente:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Recuerde que, aunque los registros notifiquen una llamada a F1, F2 y F3, a estas funciones solo se las llama *realmente* la primera vez que se encuentran. Las llamadas posteriores que se producen durante la reproducción se omiten y las salidas se reproducen en la lógica del orquestador.

Si desea registrar únicamente en la ejecución sin reproducción, puede escribir una expresión condicional para que registre solo si `IsReplaying` es `false`. Considere el ejemplo anterior, pero esta vez con comprobaciones de reproducción.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Con este cambio, la salida del registro es la siguiente:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Estado personalizado

El estado de orquestación personalizada le permite establecer un valor de estado personalizado para la función del orquestador. Este estado se proporciona a través de la API de consulta de estado HTTP o de la API `DurableOrchestrationClient.GetStatusAsync`. El estado de orquestación personalizada habilita una supervisión más completa para las funciones del orquestador. Por ejemplo, el código de la función del orquestador pueden incluir llamadas `DurableOrchestrationContext.SetCustomStatus` para actualizar el progreso de una operación de ejecución prolongada. Un cliente, como una página web u otro sistema externo, puede entonces consultar periódicamente las API de consulta de estado HTTP para obtener una información más completa del progreso. A continuación se proporciona un ejemplo en el que se usa `DurableOrchestrationContext.SetCustomStatus`:

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Mientras se ejecuta la orquestación, los clientes externos pueden capturar este estado personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Los clientes obtendrán la siguiente respuesta:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> La carga de estado personalizada se limita a 16 KB de texto JSON de UTF-16, ya que debe caber en una columna de Azure Table Storage. Puede usar almacenamiento externo si necesita una carga útil mayor.

## <a name="debugging"></a>Depuración

Azure Functions admite la depuración directa de código de función y esa misma compatibilidad se traslada a Durable Functions, tanto si se ejecuta en Azure o localmente. Sin embargo, hay algunos comportamientos que tener en cuenta al depurar:

* **Reproducción**: las funciones de orquestador suelen reproducir cuando se reciben nuevas entradas. Esto significa que una sola ejecución *lógica* de una función de orquestador puede resultar en alcanzar el mismo punto de interrupción varias veces, especialmente si se establece al principio en el código de la función.
* **Espera**: siempre que se encuentre un `await`, devuelve el control al distribuidor de Durable Task Framework. Si se trata de la primera vez que se encuentra una instrucción `await` determinada, la tarea asociada *nunca* se reanuda. En realidad, como la tarea nunca se reanuda, no se puede *omitir* la instrucción await (F10 en Visual Studio). La omisión solo funciona cuando se reproduce una tarea.
* **Tiempo de expiración de los mensajes**: Durable Functions usa mensajes de cola internos para dirigir la ejecución de las funciones tanto de orquestador como de actividad. En un entorno de varias máquinas virtuales, iniciar la depuración durante largos períodos de tiempo podría provocar que otra máquina virtual recogiera el mensaje, lo que provocaría la duplicación de la ejecución. Este comportamiento también existe para las funciones con desencadenador de cola normales, pero es importante señalarlo en este contexto, ya que las colas son un detalle de la implementación.

> [!TIP]
> Al establecer puntos de interrupción, si desea interrumpir solo la ejecución sin reproducción, puede establecer un punto de interrupción condicional que solo se active si `IsReplaying` es `false`.

## <a name="storage"></a>Storage

De forma predeterminada, Durable Functions almacena el estado en Azure Storage. Esto significa que puede inspeccionar el estado de las orquestaciones con herramientas como el [Explorador de Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de pantalla de Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Esto es útil para la depuración, porque verá el estado concreto de las orquestaciones. También se pueden examinar los mensajes de las colas para conocer el trabajo pendiente (o bloqueado, en algunos casos).

> [!WARNING]
> Aunque es conveniente ver el historial de ejecución en Table Storage, no se fie de esta tabla. Puede cambiar con el progreso de la extensión Durable Functions.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a usar los temporizadores de larga duración](durable-functions-timers.md)
