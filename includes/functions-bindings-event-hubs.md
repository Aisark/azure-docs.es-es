---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 0880d60f9cc7ca989194a98d96f9d5f118f028d0
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2019
ms.locfileid: "71692048"
---
## <a name="trigger"></a>Desencadenador

Use el desencadenador de funciones para responder a un evento enviado a una secuencia de eventos del centro de eventos. Debe tener acceso de lectura al centro de eventos subyacente para configurar el desencadenador. Cuando esta función se desencadena, el mensaje que se pasa a la función se escribe como una cadena.

## <a name="trigger---scaling"></a>Desencadenador: escalado

Cada instancia de una función de desencadenador de eventos está respaldada por una única instancia de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). El desencadenador (con tecnología de Event Hubs) garantiza que solo una instancia de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) puede obtener una concesión en una partición determinada.

Por ejemplo, considere una instancia de Event Hubs con las siguientes características:

* 10 particiones.
* 1000 eventos distribuidos uniformemente en todas las particiones, con 100 mensajes en cada partición.

Cuando se habilita la función por primera vez, solo hay una instancia de la función. Vamos a llamar a esta instancia de función `Function_0`. Laq función `Function_0` tiene una sola instancia de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) que contiene una concesión en las diez particiones. Esta instancia lee eventos de las particiones 0-9. A partir de este punto, se producirá una de las siguientes acciones:

* **No se necesitan nuevas instancias de función**: `Function_0` puede procesar los 1000 eventos antes de que la lógica de escalado de Azure Functions surta efecto. En este caso, `Function_0` procesa los 1000 mensajes.

* **Se agrega una instancia de función adicional**: Si la lógica de escalado de Azure Functions determina que `Function_0` tiene más mensajes de los que puede procesar, se crea una nueva instancia de la aplicación de función (`Function_1`). Esta nueva función también tiene asociada una instancia de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Como la instancia de Event Hubs subyacente detecta que una nueva instancia de host está tratando de leer mensajes, efectúa un equilibrio de carga en las particiones a través de las instancias de host. Por ejemplo, las particiones 0-4 pueden asignarse a `Function_0` y las particiones 5-9, a `Function_1`.

* **Se agregan N instancias de función más**: Si la lógica de escalado de Azure Functions determina que tanto `Function_0` como `Function_1` tienen más mensajes de los que pueden procesar, se crean más instancias de aplicaciones de función de `Functions_N`.  Se van creando aplicaciones hasta llegar a un punto en el que `N` es mayor que el número de particiones de centro de eventos. En nuestro ejemplo, Event Hubs vuelve a equilibrar la carga de las particiones, en este caso, entre las instancias `Function_0`...`Functions_9`.

Cuando Functions escale, `N` instancias es un número mayor que el número de particiones de centro de eventos. Esto se hace para garantizar que va a haber instancias de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) disponibles para obtener bloqueos de las particiones a medida que estén disponibles en otras instancias. Solo se le cobra por los recursos usados cuando se ejecuta la instancia de la función. En otras palabras, no se le cobrará por este aprovisionamiento en exceso.

Cuando se completa la ejecución de todas las funciones con o sin errores, se agregan puntos de comprobación a la cuenta de almacenamiento asociada. Cuando estos puntos de conexión se agregan correctamente, los 1000 mensajes ya no se vuelven a recuperar.

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](../articles/azure-functions/functions-dotnet-class-library.md) que registra el cuerpo del mensaje del desencadenador del centro de eventos.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para acceder a los [metadatos del evento](#trigger---event-metadata) en el código de función, cree un enlace a un objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requiere el uso de una instrucción para `Microsoft.Azure.EventHubs`). También puede acceder a las mismas propiedades mediante el uso de expresiones de enlace en la firma del método.  El ejemplo siguiente se muestran las dos maneras de obtener los mismos datos:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Para recibir eventos en un lote, convierta `string` o `EventData` en una matriz.  

> [!NOTE]
> Cuando se reciben en un lote, no se puede establecer un enlace a parámetros de método como en el ejemplo anterior con `DateTime enqueuedTimeUtc` y debe recibirlos desde cada objeto `EventData`.  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de script de C#](../articles/azure-functions/functions-reference-csharp.md) que usa el enlace. La función registra el cuerpo del mensaje del desencadenador de centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*.

#### <a name="version-2x"></a>Versión 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versión 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de script de C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para acceder a los [metadatos del evento](#trigger---event-metadata) en el código de función, cree un enlace a un objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requiere el uso de una instrucción para `Microsoft.Azure.EventHubs`). También puede acceder a las mismas propiedades mediante el uso de expresiones de enlace en la firma del método.  El ejemplo siguiente se muestran las dos maneras de obtener los mismos datos:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Para recibir eventos en un lote, convierta `string` o `EventData` en una matriz:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Desencadenador: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de F#](../articles/azure-functions/functions-reference-fsharp.md) que usa el enlace. La función registra el cuerpo del mensaje del desencadenador de centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. 

#### <a name="version-2x"></a>Versión 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versión 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de F#:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de JavaScript](../articles/azure-functions/functions-reference-node.md) que usa el enlace. La función lee los [metadatos del evento](#trigger---event-metadata) y registra el mensaje.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*.

#### <a name="version-2x"></a>Versión 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versión 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Para recibir eventos en un lote, establezca `cardinality` en `many` en el archivo *function.json*, como se muestra en los ejemplos siguientes.

#### <a name="version-2x"></a>Versión 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versión 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>Ejemplo de desencadenador de Python

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de Python](../articles/azure-functions/functions-reference-python.md) que usa el enlace. La función lee los [metadatos del evento](#trigger---event-metadata) y registra el mensaje.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Desencadenador: ejemplo de Java

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de Java](../articles/azure-functions/functions-reference-java.md) que usa el enlace. La función registra el cuerpo del mensaje del desencadenador del centro de eventos.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 En la [biblioteca en tiempo de ejecución de funciones de Java](/java/api/overview/azure/functions/runtime), utilice la anotación `EventHubTrigger` en los parámetros cuyo valor provendría del centro de eventos. Los parámetros con estas anotaciones hacen que la función se ejecuta cuando llega un evento.  Esta anotación se puede usar con tipos nativos de Java, POJO o valores que aceptan valores NULL mediante Optional\<T>.

## <a name="trigger---attributes"></a>Desencadenador: atributos

En las [bibliotecas de clases de C#](../articles/azure-functions/functions-dotnet-class-library.md), use el atributo [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

El constructor del atributo toma el nombre del centro de eventos, el nombre del grupo de consumidores y el nombre de una configuración de aplicación que contenga la cadena de conexión. Para obtener más información sobre estas configuraciones, vea la sección [Configuración de desencadenador](#trigger---configuration). Este es un ejemplo de atributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Para un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `EventHubTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `eventHubTrigger`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**direction** | N/D | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa el elemento de evento en el código de la función. |
|**path** |**EventHubName** | Solo Functions 1.x. El nombre del centro de eventos. Cuando el nombre del centro de eventos también está presente en la cadena de conexión, ese valor reemplaza esta propiedad en tiempo de ejecución. |
|**eventHubName** |**EventHubName** | Solo Functions 2.x. El nombre del centro de eventos. Cuando el nombre del centro de eventos también está presente en la cadena de conexión, ese valor reemplaza esta propiedad en tiempo de ejecución. |
|**consumerGroup** |**ConsumerGroup** | Una propiedad opcional que establece el [grupo de consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) que se usará para suscribirse a los eventos del centro. Si se pasa por alto, se utilizará el grupo de consumidores `$Default`. |
|**cardinalidad** | N/D | Para JavaScript. Defínalo como `many` para permitir el procesamiento por lotes.  Si se omite o se define como `one`, se pasa un único mensaje a la función. |
|**conexión** |**Connection** | El nombre de una configuración de aplicación que contenga la cadena de conexión para el espacio de nombres del centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del [espacio de nombres](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), no del propio centro de eventos. Esta cadena de conexión debe tener al menos permisos de lectura para activar el desencadenador.|
|**path**|**EventHubName**|El nombre del centro de eventos. Puede hacer referencia al nombre a través de la configuración de la aplicación `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Desencadenador: metadatos de evento

El desencadenador de Event Hubs proporciona varias [propiedades de metadatos](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Estas son propiedades de la clase [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Propiedad|Escriba|DESCRIPCIÓN|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Instancia de `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Hora de puesta en la cola en UTC.|
|`Offset`|`string`|El desplazamiento de los datos relacionados con el flujo de partición de Event Hubs. El desplazamiento es un marcador o identificador del flujo de Event Hubs. El identificador es único dentro de una partición del flujo de Event Hubs.|
|`PartitionKey`|`string`|La partición a la que se deben enviar los datos del evento.|
|`Properties`|`IDictionary<String,Object>`|Las propiedades de usuario de los datos del evento.|
|`SequenceNumber`|`Int64`|El número de secuencia de registro del evento.|
|`SystemProperties`|`IDictionary<String,Object>`|Las propiedades del sistema, incluidos los datos del evento.|

Consulte los [ejemplos de código](#trigger---example) que utilizan estas propiedades más arriba en este artículo.

## <a name="trigger---hostjson-properties"></a>Desencadenador: propiedades de host.json

El archivo [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contiene opciones de configuración que controlan el comportamiento de Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

Use el enlace de salida de Event Hubs para escribir eventos en una secuencia. Debe tener permiso de envío a un centro de eventos para escribir eventos en él.

Asegúrese de que las referencias de los paquetes necesarios están en su lugar: Functions 1.x o Functions 2.x

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](../articles/azure-functions/functions-dotnet-class-library.md) que escribe un mensaje en un centro de eventos usando el valor devuelto del método como resultado:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

En el siguiente ejemplo se muestra cómo usar la interfaz `IAsyncCollector` para enviar un lote de mensajes. Este escenario es habitual cuando se procesan mensajes procedentes de un centro de eventos y el resultado se envía a otro centro de eventos.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de script de C#](../articles/azure-functions/functions-reference-csharp.md) que usa el enlace. La función escribe un mensaje a un centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 2.x y el segundo para Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este es el código de script de C# que crea un mensaje:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Este es el código de script de C# que crea varios mensajes:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Salida: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de F#](../articles/azure-functions/functions-reference-fsharp.md) que usa el enlace. La función escribe un mensaje a un centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 2.x y el segundo para Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este es el código de F#:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de JavaScript](../articles/azure-functions/functions-reference-node.md) que usa el enlace. La función escribe un mensaje a un centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 2.x y el segundo para Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este es el código JavaScript que envía un mensaje:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Este es el código JavaScript que envía varios mensajes:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Salida: ejemplo de Python

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de Python](../articles/azure-functions/functions-reference-python.md) que usa el enlace. La función escribe un mensaje a un centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este es el código de Python que envía un solo mensaje:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Salida: ejemplo de Java

El ejemplo siguiente muestra una función de Java que escribe un mensaje que contiene la hora actual en un centro de eventos.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

En la [biblioteca en tiempo de ejecución de funciones de Java](/java/api/overview/azure/functions/runtime), utilice la anotación `@EventHubOutput` en los parámetros cuyo valor se publicaría en el centro de eventos.  El parámetro debe ser del tipo `OutputBinding<T>`, donde T es un tipo POJO o cualquier tipo nativo de Java.

## <a name="output---attributes"></a>Salida: atributos

En las [bibliotecas de clases de C#](../articles/azure-functions/functions-dotnet-class-library.md), use el atributo [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

El constructor del atributo toma el nombre del centro de eventos y el nombre de una configuración de aplicación que contenga la cadena de conexión. Para obtener más información sobre estas configuraciones, vea [Salida: configuración](#output---configuration). Este es un ejemplo de atributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `EventHub`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "eventHub". |
|**direction** | N/D | Debe establecerse en "out". Este parámetro se establece automáticamente cuando se crea el enlace en Azure Portal. |
|**name** | N/D | Nombre de la variable que se usa en el código de la función que representa el evento. |
|**path** |**EventHubName** | Solo Functions 1.x. El nombre del centro de eventos. Cuando el nombre del centro de eventos también está presente en la cadena de conexión, ese valor reemplaza esta propiedad en tiempo de ejecución. |
|**eventHubName** |**EventHubName** | Solo Functions 2.x. El nombre del centro de eventos. Cuando el nombre del centro de eventos también está presente en la cadena de conexión, ese valor reemplaza esta propiedad en tiempo de ejecución. |
|**conexión** |**Connection** | El nombre de una configuración de aplicación que contenga la cadena de conexión para el espacio de nombres del centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del *espacio de nombres*, no del propio centro de eventos. Esta cadena de conexión debe tener permisos de envío para enviar el mensaje a la secuencia de eventos.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas

En C# y scripts de C#, envíe mensajes mediante un parámetro de método, como `out string paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Para escribir varios mensajes, puede usar `ICollector<string>` o `IAsyncCollector<string>` en lugar de `out string`.

En JavaScript, puede obtener acceso al evento de salida usando `context.bindings.<name>`. `<name>` es el valor especificado en la propiedad `name` de *function.json*.

## <a name="exceptions-and-return-codes"></a>Excepciones y códigos de retorno

| Enlace | Referencia |
|---|---|
| Centro de eventos | [Guía de operaciones](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configuración de host.json

En esta sección se describen las opciones de configuración globales disponibles para este enlace en la versión 2.x. El siguiente archivo host.json de ejemplo contiene solo la configuración de la versión 2.x para este enlace. Para obtener más información acerca de las opciones de configuración globales de la versión 2.x, consulte la [referencia de host.json para Azure Functions, versión 2.x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Para obtener una referencia de host.json en Functions 1.x, consulte la [referencia de host.json para Azure Functions, versión 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------|
|maxBatchSize|64|Número máximo de eventos recibido por cada bucle de recepción.|
|prefetchCount|N/D|Valor predeterminado de PrefetchCount que utilizará el host de procesador de eventos subyacente.|
|batchCheckpointFrequency|1|Número de lotes de eventos que se va a procesar antes de crear un punto de comprobación de cursor de EventHub.|
