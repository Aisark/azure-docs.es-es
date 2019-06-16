---
title: Prueba unitaria de Azure Durable Functions
description: Obtenga información sobre cómo ejecutar una prueba unitaria de Durable Functions.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648472"
---
# <a name="durable-functions-unit-testing"></a>Prueba unitaria de Durable Functions

La prueba unitaria es parte importante de las prácticas modernas de desarrollo de software. Las pruebas unitarias comprueban el comportamiento de la lógica de negocios e impiden los cambios importantes desapercibidos en el futuro. Durable Functions puede aumentar fácilmente su complejidad, por lo que realizar pruebas unitarias ayudará a evitar los cambios importantes. En las secciones siguientes se explica cómo ejecutar una prueba unitaria de los tres tipos de función: cliente de Orchestration, Orchestrator y funciones de actividad.

## <a name="prerequisites"></a>Requisitos previos

Los ejemplos que aparecen en este artículo requieren conocer los siguientes conceptos y marcos:

* Pruebas unitarias

* Funciones duraderas

* [xUnit](https://xunit.github.io/): marco de pruebas

* [moq](https://github.com/moq/moq4): marco de simulación

## <a name="base-classes-for-mocking"></a>Clases base para simulación

La simulación se admite a través de tres clases abstractas en Durable Functions:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Estas clases son clases base para [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) y [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) que definen los métodos de cliente de Orchestration, Orchestrator y Activity. Las simulaciones establecerán el comportamiento esperado para los métodos de clase base, de manera que la prueba unitaria pueda comprobar la lógica de negocios. Hay un flujo de trabajo de dos pasos para realizar una prueba unitaria de la lógica de negocios en el cliente de Orchestration y Orchestrator:

1. Use las clases base en lugar de la implementación concreta cuando defina las firmas del cliente de Orchestration y de Orchestrator.
2. En las pruebas unitarias, simule el comportamiento de las clases base y compruebe la lógica de negocios.

Encuentre más detalles en los párrafos siguientes para probar las funciones que usan el enlace del cliente de Orchestration y el enlace de desencadenador de Orchestrator.

## <a name="unit-testing-trigger-functions"></a>Funciones de desencadenador de prueba unitaria

En esta sección, la prueba unitaria validará la lógica de la función de desencadenador HTTP siguiente para iniciar nuevas orquestaciones.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

La tarea de prueba unitaria será comprobar el valor del encabezado `Retry-After` proporcionado en la carga útil de la respuesta. Por lo tanto, la prueba unitaria simulará algunos de los métodos de [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) para garantizar el comportamiento predecible.

En primer lugar, se requiere una simulación de la clase base, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). La simulación puede ser una clase nueva que implementa [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Sin embargo, usar un marco de simulación como [moq](https://github.com/moq/moq4) simplifica el proceso:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Luego, se simula el método `StartNewAsync` para devolver un identificador de instancia conocido.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

A continuación, se simula `CreateCheckStatusResponse` para devolver siempre una respuesta HTTP 200 vacía.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

También se simula `TraceWriter`:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Ahora, se llama al método `Run` desde la prueba unitaria:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        traceWriterMock.Object);
 ```

 El último paso es comparar la salida con el valor esperado:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Después de combinar todos los pasos, la prueba unitaria tendrá el código siguiente:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funciones de Orchestrator de prueba unitaria

Las funciones de Orchestrator son incluso más interesantes para la prueba unitaria porque habitualmente tienen mucha más lógica de negocios.

En esta sección, las pruebas unitarias validarán la salida de la función `E1_HelloSequence` de Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

El código de la prueba unitaria comenzará con la creación de una simulación:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Luego, se simularán las llamadas al método de la actividad:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

A continuación, la prueba unitaria llamará al método `HelloSequence.Run`:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Finalmente, se validará la salida:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Después de combinar todos los pasos, la prueba unitaria tendrá el código siguiente:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funciones de la actividad de prueba unitaria

Es posible realizar una prueba unitaria de las funciones de actividad del mismo modo que las funciones no duraderas.

En esta sección, la prueba unitaria validará el comportamiento de la actividad de la función `E1_SayHello`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Y las pruebas unitarias comprobarán el formato de la salida. Las pruebas unitarias pueden utilizar los tipos de parámetro directamente o simular la clase [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html):

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Más información sobre moq](https://github.com/Moq/moq4/wiki/Quickstart)
