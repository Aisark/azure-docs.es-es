---
title: Inicio rápido de los flujos de dispositivos de Azure IoT Hub (versión preliminar) en Node.js para SSH y RDP | Microsoft Docs
description: En este inicio rápido, ejecutará una aplicación de Node.js de ejemplo que actúa como proxy para habilitar escenarios de SSH y RDP mediante flujos de dispositivo de IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4a6fd7dd40905a8a81a104c9d6ef22040ff88f15
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516273"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Inicio rápido: Habilitar SSH y RDP mediante un flujo de dispositivo de IoT Hub con una aplicación proxy en Node.js (versión preliminar)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub actualmente admite flujos de dispositivos como una [versión preliminar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Los flujos de dispositivo de IoT Hub](./iot-hub-device-streams-overview.md) permiten que las aplicaciones de servicio y de dispositivo se comuniquen de forma segura y apta para el firewall. 

En este inicio rápido se describe cómo ejecutar una aplicación de proxy de Node.js en el lado del servicio para permitir el envío de tráfico de Secure Shell (SSH) y Protocolo de escritorio remoto (RDP) al dispositivo mediante un flujo de dispositivo. Consulte [Ejemplo Proxy local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) para obtener información general de la configuración. 

Durante la versión preliminar pública, el SDK de Node.js solo admite flujos de dispositivo en el lado del servicio. Como resultado, en este inicio rápido solo se incluyen las instrucciones para ejecutar la aplicación de proxy local de dispositivo. Para ejecutar la aplicación de proxy local de dispositivo, consulte:  

   * [Habilitar SSH y RDP mediante un flujo de dispositivo de IoT Hub con una aplicación proxy en C](./quickstart-device-streams-proxy-c.md)
   * [Habilitar SSH y RDP mediante un flujo de dispositivo de IoT Hub con una aplicación proxy en C#](./quickstart-device-streams-proxy-csharp.md)

En este artículo se describe la configuración de SSH (mediante el puerto 22) y, a continuación, se describe cómo modificar la configuración para RDP (que usa el puerto 3389). Puesto que los flujos de dispositivo son independientes del protocolo y de la aplicación, se puede modificar el mismo ejemplo para dar cabida a otros tipos de tráfico de la aplicación cliente/servidor (normalmente mediante la modificación del puerto de comunicación).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* La versión preliminar de los flujos de dispositivo solo se admite en este momento en instancias de IoT Hub creadas en las siguientes regiones:

  * Centro de EE. UU.
  * EUAP del centro de EE. UU.

* Para ejecutar la aplicación local del servicio en este inicio rápido se necesita la versión 10.x.x de Node.js o posterior en la máquina de desarrollo.
  * Descargue [Node.js](https://nodejs.org) para varias plataformas.
  * Compruebe la versión actual de Node.js en la máquina de desarrollo con el comando siguiente:

   ```
   node --version
   ```

* Ejecute el siguiente comando para agregar la extensión IoT de Azure para la CLI de Azure a la instancia de Cloud Shell. La extensión IOT agrega comandos específicos de IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) a la CLI de Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Si aún no lo ha hecho, [descargue el proyecto de Node.js de ejemplo](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) y extraiga el archivo ZIP.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Si ha completado la anterior [Guía de inicio rápido: Envío de telemetría desde un dispositivo a un centro de IoT](quickstart-send-telemetry-node.md) puede omitir este paso.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrar un dispositivo

Si ha completado [Inicio rápido: Envío de telemetría desde un dispositivo a un centro de IoT](quickstart-send-telemetry-node.md) puede omitir este paso.

Debe registrar un dispositivo con IoT Hub antes de poder conectarlo. En esta sección, usará Azure Cloud Shell para registrar un dispositivo simulado.

1. Ejecute el siguiente comando en Cloud Shell para crear la identidad del dispositivo:

   > [!NOTE]
   > * Reemplace el marcador de posición *YourIoTHubName* por el nombre que eligió para su centro de IoT.
   > * Para el nombre del dispositivo que va a registrar se recomienda usar *MyDevice* , tal como se muestra. Si elige otro nombre para el dispositivo, úselo en todo el artículo y actualice el nombre del dispositivo en las aplicaciones de ejemplo antes de ejecutarlas.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. También necesita una *cadena de conexión de servicio* para permitir que la aplicación back-end se conecte a IoT Hub y recupere los mensajes. El siguiente comando recupera esta cadena para el centro de IoT:

   > [!NOTE]
   > Reemplace el marcador de posición *YourIoTHubName* por el nombre que eligió para su centro de IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Anote la cadena de conexión del servicio devuelta para usarla más adelante en este inicio rápido. Debe ser similar al ejemplo siguiente:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Conexión mediante SSH a un dispositivo mediante flujos de dispositivo

En esta sección, establece una secuencia de un extremo a otro para tunelizar el tráfico SSH.

### <a name="run-the-device-local-proxy-application"></a>Ejecución de la aplicación de proxy local de dispositivo

Como ya se ha mencionado, el SDK de IoT Hub para Node.js solo admite flujos de dispositivo en el lado del servicio. Para la aplicación de dispositivo local, use una aplicación de proxy de dispositivo que esté disponible en uno de los inicios rápidos siguientes:

   * [Habilitar SSH y RDP mediante un flujo de dispositivo de IoT Hub con una aplicación proxy en C](./quickstart-device-streams-proxy-c.md)
   * [Habilitar SSH y RDP mediante un flujo de dispositivo de IoT Hub con una aplicación proxy en C#](./quickstart-device-streams-proxy-csharp.md) 

Antes de continuar con el paso siguiente, asegúrese de que se está ejecutando la aplicación de proxy de dispositivo local.

### <a name="run-the-service-local-proxy-application"></a>Ejecución de la aplicación de proxy local de servicio

Con la aplicación de proxy local de dispositivo en ejecución, ejecute la aplicación de proxy local de servicio escrita en Node.js. Para ello, haga lo siguiente en una ventana de terminal local:

1. Como variables de entorno proporcione las credenciales del servicio, el identificador de dispositivo de destino donde se ejecuta el demonio de SSH y el número de puerto del proxy que se ejecuta en el dispositivo.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Cambie el marcador de posición ServiceConnectionString para que coincida con la cadena de conexión del servicio y **MyDevice**  para que coincida con el identificador del dispositivo, si ha asignado otro nombre al suyo.

1. Vaya al directorio `Quickstarts/device-streams-service` en la carpeta del proyecto descomprimida. Use el código siguiente para ejecutar la aplicación proxy local de servicio:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Conexión mediante SSH al dispositivo mediante flujos de dispositivo

En Linux, ejecute SSH mediante `ssh $USER@localhost -p 2222` en un terminal. En Windows, use su cliente SSH favorito (por ejemplo, PuTTY).

Salida de la consola en el servicio local una vez establecida la sesión SSH (la aplicación de proxy local de servicio escucha en el puerto 2222):

![Salida del terminal de SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Salida de la consola de la aplicación cliente de SSH (el cliente de SSH se comunica con el demonio de SSH mediante la conexión al puerto 22, en el que escucha la aplicación de proxy local de servicio):

![Resultado del cliente de SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Conexión mediante RDP al dispositivo mediante flujos de dispositivo

Ahora utilice la aplicación cliente de RDP y conéctese a la aplicación de proxy de servicio en el puerto 2222 (este era un puerto arbitrario que eligió anteriormente).

> [!NOTE]
> Asegúrese de que el proxy de dispositivo está configurado correctamente para RDP y con el puerto 3389 de RDP.

![El cliente RDP se conecta a la aplicación de proxy local de servicio](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido se ha configurado un centro de IoT, se ha registrado un dispositivo y se ha implementado una aplicación proxy de servicio para habilitar RDP y SSH en un dispositivo IoT. El tráfico RDP y SSH se canalizará a través de un flujo de dispositivo al centro de IoT. Este proceso elimina la necesidad de conectividad directa al dispositivo.

Para más información sobre los flujos de dispositivo, consulte:

> [!div class="nextstepaction"]
> [Introducción a los flujos de dispositivo](./iot-hub-device-streams-overview.md)
