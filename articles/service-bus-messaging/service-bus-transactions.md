---
title: Introducción al procesamiento de transacciones en Azure Service Bus| Microsoft Docs
description: Información general sobre las transacciones atómicas de Azure Service Bus y la característica "enviar por"
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2018
ms.author: aschhab
ms.openlocfilehash: a839a4cad824a74bde388317cf3aaddf9c5bd47f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332353"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Información general sobre el procesamiento de transacciones de Service Bus

En este artículo se describen las funcionalidades de transacciones de Microsoft Azure Service Bus. Gran parte de la discusión se ilustra la [AMQP ejemplo de transacciones con Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Este artículo se limita a proporcionar información general sobre el procesamiento de transacciones y la característica *Enviar por* de Service Bus, mientras que el ejemplo de transacciones atómicas es más amplio y con un ámbito más complejo.

## <a name="transactions-in-service-bus"></a>Transacciones en Service Bus

Una *transacción* agrupa dos o más operaciones en un *ámbito de ejecución*. Por naturaleza, una transacción de este tipo debe garantizar que todas las operaciones que pertenecen a un grupo determinado de operaciones tendrán éxito o darán error de forma conjunta. En este sentido, las transacciones actúan como una unidad, lo que a menudo se conoce como *atomicidad*.

Service Bus es un agente de mensajes transaccional, y garantiza la integridad transaccional de todas las operaciones internas en sus almacenes de mensajes. Todas las transferencias de mensajes dentro de Service Bus, como mover mensajes a una [cola de mensajes fallidos](service-bus-dead-letter-queues.md) o [reenviar automáticamente](service-bus-auto-forwarding.md) mensajes entre entidades, son transaccionales. Por lo tanto, si Service Bus acepta un mensaje, es que ya se ha almacenado y etiquetado con un número de secuencia. Desde ese momento, todas las transferencias de mensajes dentro de Service Bus son operaciones coordinadas entre entidades, y ninguna de ellas dará lugar a la pérdida (el origen tiene éxito y el destino da error) o a la desduplicación (el origen da error y el destino tiene éxito) del mensaje.

Service Bus admite operaciones de agrupación en una sola entidad de mensajería (cola, tema, suscripción) dentro del ámbito de una transacción. Por ejemplo, puede enviar varios mensajes a una cola desde dentro de un ámbito de transacción y los mensajes solo se confirmarán en el registro de la cola cuando la transacción se complete correctamente.

## <a name="operations-within-a-transaction-scope"></a>Operaciones dentro de un ámbito de transacción

Las operaciones que pueden realizarse dentro de un ámbito de transacción son las siguientes:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender) y [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Send, SendAsync, SendBatch y SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock y RenewLockAsync 

Las operaciones de recepción no se incluyen, porque se supone que la aplicación captura mensajes mediante el modo [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode), dentro de algún bucle de recepción o con una devolución de llamada [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage), y solo entonces se abre un ámbito de transacción para procesar el mensaje.

La disposición del mensaje (completar, abandonar, correo devuelto, aplazar), se produce entonces dentro del ámbito del resultado general de la transacción y depende de este.

## <a name="transfers-and-send-via"></a>Transferencias y "enviar por"

Para permitir la entrega transaccional de datos de una cola a un procesador y luego a otra cola, Service Bus admite *transferencias*. En una operación de transferencia, un remitente envía primero un mensaje a una *cola de transferencia* y esta mueve inmediatamente el mensaje a la cola de destino deseada usando la misma implementación de transferencias sólida que la funcionalidad de reenvío automático en la que se basa. El mensaje nunca se confirma en el registro de la cola de transferencia de forma que se vuelve visible para los consumidores de dicha cola.

La eficacia de esta funcionalidad transaccional se hace evidente cuando la propia cola de transferencia es el origen de los mensajes de entrada del remitente. En otras palabras, Service Bus puede transferir el mensaje a la cola de destino "mediante" la cola de transferencia y al mismo tiempo realizar una operación completa (o de aplazamiento o correo devuelto) en el mensaje de entrada, todo en una operación atómica. 

### <a name="see-it-in-code"></a>Verlo en el código

Para configurar tales transferencias, se crea el remitente de un mensaje que se dirige a la cola de destino mediante la cola de transferencia. También hay un receptor que extrae los mensajes de esa misma cola. Por ejemplo: 

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Una transacción sencilla, a continuación, utiliza los siguientes elementos, como en el ejemplo siguiente. Para hacer referencia en el ejemplo completo, consulte el [código fuente en GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para más información sobre las colas de Service Bus:

* [Utilización de las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Encadenamiento de entidades de Service Bus con reenvío automático](service-bus-auto-forwarding.md)
* [Ejemplo de reenvío automático](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Ejemplo de transacciones atómicas con Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Comparación de colas de Azure y colas de Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


