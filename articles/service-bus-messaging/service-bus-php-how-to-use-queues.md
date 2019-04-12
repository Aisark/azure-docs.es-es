---
title: Uso de colas de Service Bus con PHP | Microsoft Docs
description: Obtenga información acerca de cómo usar las colas de Service Bus en Azure. Ejemplos de código escritos en PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 55eee839e24db2ad96eb635adc488e9a119c5907
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501202"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Uso de colas de Service Bus con PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

En este tutorial, aprenderá a crear aplicaciones de PHP para enviar y recibir mensajes de una cola de Service Bus. 

## <a name="prerequisites"></a>Requisitos previos
1. Una suscripción de Azure. Para completar este tutorial, deberá tener una cuenta de Azure. Puede activar su [ventajas de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarse para obtener un [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Si no tiene una cola para que funcione con, siga los pasos de la [uso de Azure portal para crear una cola de Service Bus](service-bus-quickstart-portal.md) artículo para crear una cola.
    1. Leer el breve **Introducción** de Service Bus **colas**. 
    2. Creación de un Bus de servicio **espacio de nombres**. 
    3. Obtener el **cadena de conexión**. 

        > [!NOTE]
        > Creará un **cola** en el espacio de nombres de Service Bus mediante PHP en este tutorial. 
3. [SDK de Azure para PHP](../php-download-sdk.md)

## <a name="create-a-php-application"></a>Creación de una aplicación PHP
El único requisito a la hora de crear una aplicación PHP para obtener acceso a Azure Blob service es que el código haga referencia a clases de [Azure SDK para PHP](../php-download-sdk.md) dentro del código. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, o bien el Bloc de notas.

> [!NOTE]
> La instalación de PHP debe tener también la [extensión OpenSSL](https://php.net/openssl) instalada y habilitada.

En esta guía, usará las características de servicio, que pueden llamarse desde dentro de una aplicación PHP localmente o en el código que se ejecuta dentro de un rol web de Azure, el rol de trabajo o el sitio Web.

## <a name="get-the-azure-client-libraries"></a>Obtención de las bibliotecas de clientes de Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configuración de la aplicación para usar Service Bus
Para usar las API de la cola de Service Bus de Azure, siga estos pasos:

1. Haga referencia al archivo autocargador mediante la instrucción [require_once][require_once].
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase `ServicesBuilder`.

> [!NOTE]
> En este ejemplo (así como en otros ejemplos de este artículo), se asume que ha instalado las bibliotecas de clientes PHP para Azure mediante el compositor. Si las instaló las bibliotecas manualmente o como un paquete PEAR, debe hacer referencia al archivo autocargador **WindowsAzure.php**.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

En los ejemplos que aparecen a continuación, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

## <a name="set-up-a-service-bus-connection"></a>Configuración de una conexión de Service Bus
Para crear una instancia de un cliente de Service Bus, primero debe disponer de una cadena de conexión válida con el siguiente formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Donde `Endpoint` tiene generalmente el formato `[yourNamespace].servicebus.windows.net`.

Para crear cualquier cliente de servicio de Azure, debe usar la clase `ServicesBuilder`. Puede:

* pasarle directamente la cadena de conexión, o bien
* usar **CloudConfigurationManager (CCM)** para buscar la cadena de conexión en varios orígenes externos:
  * De manera predeterminada, admite un origen externo: variables de entorno.
  * Puede agregar nuevos orígenes extendiendo la clase `ConnectionStringSource`.

En los ejemplos descritos aquí, la cadena de conexión se pasa directamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Creación de una cola
Puede realizar operaciones de administración para colas de Service Bus mediante la clase `ServiceBusRestProxy`. Un objeto `ServiceBusRestProxy` se construye mediante el método de generador `ServicesBuilder::createServiceBusService` con una cadena de conexión adecuada que encapsula los permisos de token para administrarlo.

En el ejemplo siguiente se muestra cómo crear una instancia de un objeto `ServiceBusRestProxy` y llamar a `ServiceBusRestProxy->createQueue` para crear una cola denominada `myqueue` dentro de un espacio de nombres de servicios `MySBNamespace`:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Puede usar el método `listQueues` en los objetos `ServiceBusRestProxy` para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres.
> 
> 

## <a name="send-messages-to-a-queue"></a>mensajes a una cola
Para enviar un mensaje a una cola de Service Bus, su aplicación llama al método `ServiceBusRestProxy->sendQueueMessage`. El código siguiente muestra cómo enviar un mensaje a la cola `myqueue` que hemos creado anteriormente en el espacio de nombres de servicio `MySBNamespace`.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Los mensajes enviados a (desde) Service Bus y recibidos las colas son instancias de la [BrokeredMessage] [ BrokeredMessage] clase. Los objetos [BrokeredMessage][BrokeredMessage] tienen un conjunto de métodos y propiedades estándar que se usan para retener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de la aplicación.

El tamaño máximo de mensaje que admiten las colas de Service Bus es de 256 KB en el [nivel Estándar](service-bus-premium-messaging.md) y de 1 MB en el [nivel Premium](service-bus-premium-messaging.md). El encabezado, que incluye propiedades de la aplicación estándar y personalizadas, puede tener un tamaño máximo de 64 KB. No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. Este límite superior para el tamaño de la cola es de 5 GB.

## <a name="receive-messages-from-a-queue"></a>mensajes de una cola

La mejor forma de recibir mensajes desde una cola es usar un método `ServiceBusRestProxy->receiveQueueMessage`. Los mensajes se pueden recibir de dos modos distintos: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) y [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** es el valor predeterminado.

Al usar el modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode), la operación de recepción consta de una sola fase; es decir, cuando Service Bus recibe una solicitud de lectura para un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como Service Bus habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock), la recepción de un mensaje se convierte en una operación de dos etapas, lo que hace posible admitir aplicaciones que no pueden tolerar la pérdida de mensajes. Cuando Service Bus recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción mediante la realización de una llamada a `ServiceBusRestProxy->deleteMessage` en el mensaje recibido. Cuando Service Bus ve la llamada `deleteMessage`, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se puede recibir y procesar un mensaje usando el modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (el modo predeterminado).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

Service Bus proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, puede llamar al método `unlockMessage` en el mensaje recibido (en lugar de al método `deleteMessage`). Esto hará que Service Bus desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces Service Bus desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje, pero antes de realizar la solicitud `deleteMessage`, el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esta posibilidad habitualmente se denomina *Al menos un procesamiento*, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado,entonces se recomienda la incorporación de lógica adicional a la aplicación para administrar la entrega de mensajes duplicados. A menudo, esto se consigue con la propiedad `getMessageId` del mensaje, que permanece constante en todos los intentos de entrega.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya conoce los aspectos básicos de las colas de Service Bus, consulte [Colas, temas y suscripciones][Queues, topics, and subscriptions], donde encontrará más información.

Para más información, visite también el [Centro para desarrolladores de PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


