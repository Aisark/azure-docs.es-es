---
title: Mensajes de nube a dispositivo con IoT Hub de Azure (.NET) | Microsoft Docs
description: Cómo enviar mensajes de nube a un dispositivo de una instancia de IoT Hub de Azure mediante los SDK de IoT de Azure para .NET. Modifique una aplicación de dispositivo para recibir mensajes de nube a dispositivo y cambie una aplicación de back-end para enviar los mensajes de nube a dispositivo.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 0d83bdc3fd3f644013a2d2b80128839658524db9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65864448"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Envío de mensajes desde la nube al dispositivo con IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introducción

IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end. El artículo [Enviar datos de telemetría desde un dispositivo a IoT Hub](quickstart-send-telemetry-dotnet.md) muestra cómo crear una instancia de IoT Hub, aprovisionar la identidad de un dispositivo y codificar una aplicación de dispositivo que envíe mensajes del dispositivo a la nube.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se basa en la guía de inicio rápido [Enviar datos de telemetría desde un dispositivo a IoT Hub](quickstart-send-telemetry-dotnet.md). Muestra cómo completar los pasos siguientes:

* Desde el back-end de la nube de la aplicación, envíe mensajes de nube a dispositivo en un único dispositivo a través de IoT Hub.

* Reciba mensajes de nube a dispositivo en un dispositivo.

* Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde IoT Hub.

Encontrará más información sobre los mensajes de nube a dispositivo en [Mensajería de dispositivo a nube y de nube a dispositivo con IoT Hub](iot-hub-devguide-messaging.md).

Al final de este tutorial, ejecutará dos aplicaciones de consola de .NET.

* **SimulatedDevice**, una versión modificada de la aplicación que se creó en [Enviar datos de telemetría desde un dispositivo a IoT Hub](quickstart-send-telemetry-dotnet.md), que se conecta a IoT Hub y recibe mensajes de la nube al dispositivo.

* **SendCloudToDevice**, que envía un mensaje de nube a dispositivo a la aplicación de dispositivo mediante IoT Hub y, luego, recibe su confirmación de entrega.

> [!NOTE]
> IoT Hub ofrece compatibilidad con SDK para muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante [SDK de dispositivos IoT de Azure](iot-hub-devguide-sdks.md). Para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general a Azure IoT Hub consulte la [Guía para desarrolladores de IoT Hub](iot-hub-devguide.md).
> 

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio

* Una cuenta de Azure activa. (En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos).

## <a name="receive-messages-in-the-device-app"></a>Recepción de mensajes en la aplicación de dispositivo

En esta sección, modificará la aplicación de dispositivo que creó en [Enviar datos de telemetría desde un dispositivo a IoT Hub](quickstart-send-telemetry-dotnet.md) para recibir mensajes de nube a dispositivo de IoT Hub.

1. En Visual Studio, en el proyecto **SimulatedDevice**, agregue el método siguiente a la clase **Program**.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

   El método `ReceiveAsync` devuelve de forma asincrónica el mensaje recibido en el momento en que lo recibe el dispositivo. Se devuelve *null* tras un período de espera que se puede especificar (en este caso, se usa el valor predeterminado de un minuto). Cuando la aplicación recibe un valor *null*, debe continuar esperando nuevos mensajes. Este requisito es el motivo de la línea `if (receivedMessage == null) continue`.

    La llamada a `CompleteAsync()` notifica al Centro de IoT que el mensaje se ha procesado correctamente. Los mensajes pueden quitarse con seguridad de la cola del dispositivo. Si ha sucedido algo que ha impedido que la aplicación del dispositivo termine de procesar el mensaje, el Centro de IoT lo entrega de nuevo. Es importante que la lógica de procesamiento de mensajes de la aplicación de dispositivo sea *idempotente*, de modo que, si se recibe el mismo mensaje varias veces, se genera el mismo resultado. 

    Una aplicación también puede abandonar temporalmente un mensaje, lo que da lugar a que el Centro de IoT retenga el mensaje en la cola para su consumo futuro. O bien, la aplicación puede rechazar un mensaje, de forma que este se quita permanentemente de la cola. Para más información sobre el ciclo de vida de los mensajes de nube a dispositivo, consulte [Mensajería de dispositivo a nube y de nube a dispositivo con IoT Hub](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Cuando se usa HTTP en lugar de MQTT o AMQP como transporte, el método `ReceiveAsync` se devuelve inmediatamente. El patrón admitido para los mensajes de nube a dispositivo con HTTP es dispositivos conectados de forma intermitente que buscan mensajes con poca frecuencia (menos de cada 25 minutos). Emitir más recepciones HTTP tendrá como resultado la limitación de solicitudes de IoT Hub. Para más información sobre las diferencias entre la compatibilidad con MQTT, AMQP y HTTPS, y la limitación de IoT Hub, consulte [Mensajería de dispositivo a nube y de nube a dispositivo con IoT Hub](iot-hub-devguide-messaging.md).
   >

2. Agregue el siguiente método en el método **Main**, inmediatamente delante de la línea `Console.ReadLine()`:

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>Obtener la cadena de conexión de IoT Hub

En primer lugar, recupere la cadena de conexión de IoT Hub del portal.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione **Grupos de recursos**.

2. Seleccione el grupo de recursos que esté usando en este procedimiento.

3. Seleccione el IoT Hub que esté usando.

4. En el panel del centro, seleccione **Directivas de acceso compartido**.

5. seleccione **iothubowner**. Muestra las cadenas de conexión en el panel **iothubowner**. Seleccione el icono de copia para la **cadena de conexión - clave principal**. Guarde la cadena de conexión para usarla más adelante.

   ![Obtener la cadena de conexión de IoT Hub](./media/iot-hub-csharp-csharp-c2d/get-iot-hub-connection-string.png)

## <a name="send-a-cloud-to-device-message"></a>Envío de mensajes de nube a dispositivo

Ahora debe escribir una aplicación de consola de .NET que envía mensajes de la nube al dispositivo a la aplicación de dispositivo.

1. En la solución actual de Visual Studio, haga clic con el botón derecho en la solución y seleccione Agregar> Nuevo proyecto. Seleccione el **escritorio de Windows** y luego **Aplicación de consola (.NET Framework)** . Nombre el proyecto **SendCloudToDevice** y seleccione la versión más reciente de .NET Framework; a continuación, seleccione **Aceptar** para crear el proyecto.

   ![Nuevo proyecto en Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes de NuGet para la solución...** .

   Esta acción abre la ventana **Administrar paquetes NuGet**.

3. Busque **Microsoft.Azure.Devices** y seleccione la pestaña Examinar. Cuando encuentre el paquete, haga clic en **Instala** y acepte las condiciones de uso.

   De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de Servicios IoT de Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Agregue la instrucción `using` en la parte superior del archivo **Program.cs**.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor del marcador de posición con la cadena de conexión de IoT Hub que guardó anteriormente en esta sección. 

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Agregue el método siguiente a la clase **Program**. Escriba el nombre del dispositivo que usó al definir el dispositivo en [Enviar telemetría desde un dispositivo a IoT Hub...](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myDevice", commandMessage);
   }
   ```

   Este método envía un nuevo mensaje de la nube al dispositivo con el identificador `myFirstDevice`. Cambie este parámetro solo si lo modificó con respecto al utilizado en [Enviar datos de telemetría desde un dispositivo a IoT Hub](quickstart-send-telemetry-dotnet.md).

7. Finalmente, agregue las líneas siguientes al método **Main** .

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. Desde Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y elija la acción **Iniciar** para **ReadDeviceToCloudMessages**, **SimulatedDevice** y **SendCloudToDevice**.

9. Presione **F5**. Deberían iniciarse las tres aplicaciones. Seleccione la ventana **SendCloudToDevice** y presione **Intro**. Debería ver el mensaje que recibe la aplicación de dispositivo.

   ![Aplicación que recibe el mensaje](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Recepción de comentarios de entrega

Es posible solicitar confirmaciones de entrega (o expiración) del Centro de IoT para cada mensaje de nube a dispositivo. Esta opción permite que el back-end de solución notifique fácilmente la lógica de reintento o compensación. Para más información sobre los comentarios de nube a dispositivo, consulte [Mensajería de dispositivo a nube y de nube a dispositivo con IoT Hub](iot-hub-devguide-messaging.md).

En esta sección, modificará la aplicación **SendCloudToDevice** para solicitar comentarios y recibirlos de IoT Hub.

1. En Visual Studio, en el proyecto **SendCloudToDevice**, agregue el método siguiente a la clase **Program**.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Tenga en cuenta que este patrón de recepción es el mismo que se usa para recibir mensajes de nube a dispositivo desde la aplicación de dispositivo.

2. Agregue el siguiente método en **Main** inmediatamente después de la línea `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Para solicitar comentarios de la entrega del mensaje de la nube a un dispositivo, debe especificar una propiedad en el método **SendCloudToDeviceMessageAsync** . Agregue la línea siguiente, inmediatamente después de la línea `var commandMessage = new Message(...);`.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Ejecute las aplicaciones presionando **F5**. Debería ver que se inician las tres aplicaciones. Seleccione la ventana **SendCloudToDevice** y presione **Intro**. Verá los mensajes que recibe la aplicación de dispositivo y, al cabo de unos segundos, el mensaje de comentarios que recibe la aplicación **SendCloudToDevice**.

   ![Aplicación que recibe el mensaje](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo [Control de errores transitorios](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Pasos siguientes

En este procedimiento, ha aprendido a enviar y recibir mensajes de nube a dispositivo.

Para ver ejemplos de soluciones de un extremo a otro que usen IoT Hub, vea el [Acelerador de la solución de supervisión remota de Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía para desarrolladores de IoT Hub](iot-hub-devguide.md).