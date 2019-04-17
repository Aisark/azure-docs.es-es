---
title: 'Control de eventos externos con Durable Functions: Azure'
description: Aprenda a controlar eventos externos en la extensión Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: eb024e11b78d13d5ab4544c634acef2ade8141c2
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608990"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Control de eventos externos con Durable Functions (Azure Functions)

Las funciones de orquestador tienen la capacidad de esperar y escuchar eventos externos. Esta característica de [Durable Functions](durable-functions-overview.md) suele ser útil para controlar las interacciones humanas u otros desencadenadores externos.

> [!NOTE]
> Los eventos externos son operaciones asincrónicas unidireccionales. No resultan adecuados para situaciones donde el cliente que envía el evento necesita una respuesta sincrónica de la función de orquestador.

## <a name="wait-for-events"></a>Espera de eventos

El método [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) permite que una función de orquestador espere y escuche un evento externo de forma asincrónica. La función de orquestador de escucha declara el *nombre* del evento y la *forma de los datos* que espera recibir.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

El ejemplo anterior escucha un evento único específico y toma medidas cuando se recibe.

Puede escuchar varios eventos al mismo tiempo, al igual que en el ejemplo siguiente, que espera una de tres notificaciones de eventos posibles.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

El ejemplo anterior escucha *cualquiera* de varios eventos posibles. También es posible esperar *todos* los eventos.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) espera indefinidamente alguna entrada.  La aplicación de función puede descargarse con seguridad mientras espera. En el momento en que un evento llega a esta instancia de orquestación, esta se activa automáticamente y procesa de inmediato el evento.

> [!NOTE]
> Si la aplicación de función usa el plan de consumo, no se aplican costos de facturación mientras una función de orquestador espera una tarea de `WaitForExternalEvent` (.NET) o `waitForExternalEvent` (JavaScript), independientemente de cuánto tiempo espere.

En .NET, si la carga del evento no se puede convertir al tipo `T` esperado, se produce una excepción.

## <a name="send-events"></a>Envío de eventos

El método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) envía los eventos que `WaitForExternalEvent` (.NET) o `waitForExternalEvent` (JavaScript) espera.  El método `RaiseEventAsync` toma *eventName* y *eventData* como parámetros. Los datos del evento deben poderse serializar con JSON.

A continuación se muestra una función desencadenada por la cola de ejemplo que envía un evento de aprobación a una instancia de la función de orquestador. El identificador de la instancia de orquestación procede del cuerpo del mensaje de la cola.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internamente, `RaiseEventAsync` (.NET) o `raiseEvent` (JavaScript) pone en cola un mensaje que la función de orquestador en espera selecciona. Si la instancia no está esperando el *nombre de evento* especificado, el mensaje del evento se agrega a una cola en memoria. Si la instancia de orquestación inicia posteriormente la escucha de ese *nombre de evento*, se comprobará si hay mensajes de eventos en la cola.

> [!NOTE]
> Si no hay ninguna instancia de orquestación con el *identificador de instancia* especificado, se descartará el mensaje del evento. Para más información acerca de este comportamiento, consulte el [problema de GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> Al desarrollar de forma local en JavaScript, deberá establecer la variable de entorno `WEBSITE_HOSTNAME` en `localhost:<port>`, por ejemplo `localhost:7071` para usar métodos en `DurableOrchestrationClient`. Para obtener más información sobre este requisito, vea el [problema de GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información acerca de cómo configurar orquestaciones infinitas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Ejecutar un ejemplo que espera eventos externos](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Ejecutar un ejemplo que espera interacción humana](durable-functions-phone-verification.md)