---
title: 'Inicio rápido: Uso de Azure PowerShell para crear una cola de Service Bus | Microsoft Docs'
description: En este inicio rápido, aprenderá a utilizar Azure PowerShell para crear una cola de Service Bus. Después, usará una aplicación de ejemplo para enviar mensajes a la cola y recibirlos desde ella.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 143c36df623085eb4f07363d9c9ebd64d4f5a144
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104767"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>Inicio rápido: Uso de Azure PowerShell para crear una cola de Service Bus
Microsoft Azure Service Bus es un agente de mensajes de integración empresarial que proporciona mensajería segura y una absoluta confiabilidad. Un escenario típico de Service Bus implica normalmente el desacoplamiento de dos o más aplicaciones, servicios o procesos, y la transferencia de los cambios de estado o de datos. Estos escenarios pueden implicar la programación de varios trabajos por lotes en otras aplicaciones o servicios, o desencadenar la realización de pedidos. Por ejemplo, una empresa minorista puede enviar los datos de un punto de ventas a un área de operaciones o centro de distribución regional para indicar las actualizaciones de reposición y de inventario. En este escenario, la aplicación cliente envía y recibe mensajes de una cola de Service Bus.

![queue](./media/service-bus-quickstart-powershell/quick-start-queue.png)

Esta guía de inicio rápido describe cómo enviar y recibir mensajes de una cola de Service Bus, cómo usar PowerShell para crear un espacio de nombres de mensajería y una cola dentro de este, y cómo obtener las credenciales de autorización de ese espacio de nombres. El procedimiento muestra después cómo enviar y recibir mensajes de esta cola mediante la [biblioteca de .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita][] antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de que tiene instalados los siguientes elementos:

- [Visual Studio 2017 Update 3 (versión 15.3, 26730.01)](https://www.visualstudio.com/vs) o posterior.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versión 2.0 o posterior.

Para esta guía de inicio rápido es necesario ejecutar la versión más reciente de Azure PowerShell. Si necesita instalarlo o actualizarlo, consulte [Instalación y configuración de Azure PowerShell][].

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

1. En primer lugar, instale el módulo de PowerShell de Service Bus, si no lo ha hecho anteriormente:

   ```azurepowershell-interactive
   Install-Module AzureRM.ServiceBus
   ```

2. Ejecute el siguiente comando para iniciar sesión en Azure:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

3. Emita los comandos siguientes para establecer el contexto de la suscripción actual o ver la suscripción que está activa actualmente:

   ```azurepowershell-interactive
   Select-AzureRmSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzureRmContext
   ```

## <a name="provision-resources"></a>Aprovisionamiento de recursos

En el símbolo del sistema de PowerShell, emita los comandos siguientes para aprovisionar recursos de Service Bus. Asegúrese de reemplazar todos los marcadores de posición por los valores apropiados:

```azurepowershell-interactive
# Create a resource group 
New-AzureRmResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzureRmServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzureRmServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Después de la ejecución del cmdlet `Get-AzureRmServiceBusKey`, copie y pegue la cadena de conexión y el nombre de cola que ha seleccionado en una ubicación temporal como, por ejemplo, el Bloc de notas. Esta información la necesitará en el siguiente paso.

## <a name="send-and-receive-messages"></a>Envío y recepción de mensajes

Una vez que haya creado el espacio de nombres y la cola, y disponga de las credenciales necesarias, estará listo para enviar y recibir mensajes. Puede examinar el código en [esta carpeta de ejemplos de GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Para ejecutar el código, haga lo siguiente:

1. Clone el [repositorio de GitHub de Service Bus](https://github.com/Azure/azure-service-bus/) mediante la emisión del comando siguiente:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Vaya a la carpeta de ejemplos `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Si aún no lo ha hecho, obtenga la cadena de conexión mediante el siguiente cmdlet de PowerShell. No olvide reemplazar `my-resourcegroup`  y  `namespace-name` por sus valores específicos: 

   ```azurepowershell-interactive
   Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. En el símbolo del sistema de PowerShell, escriba el siguiente comando:

   ```shell
   dotnet build
   ```

6. Vaya a la carpeta `bin\Debug\netcoreapp2.0`.

7. Escriba el siguiente comando para ejecutar el programa. No olvide reemplazar `myConnectionString` por el valor que obtuvo previamente, y `myQueueName` por el nombre de la cola que creó:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. Observe que se envían 10 mensajes a la cola y que, posteriormente, se reciben de ella:

   ![salida del programa](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Ejecute el siguiente comando para quitar el grupo de recursos, el espacio de nombres y todos los recursos relacionados:

```powershell-interactive
Remove-AzureRmResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Descripción del código de ejemplo

Esta sección contiene información más detallada acerca de lo que hace el código de ejemplo. 

### <a name="get-connection-string-and-queue"></a>Obtención de la cadena de conexión y la cola

La cadena de conexión y el nombre de la cola se pasan al método `Main()` como argumentos de la línea de comandos. `Main()` declara dos variables de cadena que contienen estos valores:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
El método `Main()` inicia entonces el bucle de mensajes asincrónicos `MainAsync()`.

### <a name="message-loop"></a>Bucle de mensajes

El método MainAsync() crea un cliente de cola con los argumentos de la línea de comandos, llama a un controlador de mensajes de recepción denominado `RegisterOnMessageHandlerAndReceiveMessages()` y envía el conjunto de mensajes:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

El método `RegisterOnMessageHandlerAndReceiveMessages()` simplemente establece unas cuantas opciones del controlador de mensajes y, a continuación, llama al método `RegisterMessageHandler()` del cliente de la cola que inicia la recepción:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Envío de mensajes

Se producen las operaciones de creación y envío de mensajes del método `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Procesamiento de mensajes

El método `ProcessMessagesAsync()` confirma, procesa y completa la recepción de los mensajes:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha creado un espacio de nombres de Service Bus y otros recursos necesarios para enviar y recibir mensajes de una cola. Para más información sobre cómo escribir código para enviar y recibir mensajes, pase al tutorial siguiente de Service Bus:

> [!div class="nextstepaction"]
> [Actualización del inventario mediante Azure PowerShell](./service-bus-tutorial-topics-subscriptions-powershell.md)

[cuenta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalación y configuración de Azure PowerShell]: /powershell/azure/azurerm/install-azurerm-ps