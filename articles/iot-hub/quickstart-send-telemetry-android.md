---
title: Guía de inicio rápido para enviar datos de telemetría a Azure IoT Hub (Android) | Microsoft Docs
description: En esta guía de inicio rápido va a ejecutar una aplicación Android de ejemplo para enviar datos de telemetría simulados a IoT Hub y para leer los datos de telemetría procedentes de este para procesarlos en la nube.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: b8cf0891bd2a11a4ea46cc9fb8bad266862b6971
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005378"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Inicio rápido: Envío de datos de telemetría de IoT desde un dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub es un servicio de Azure que le permite ingerir grandes volúmenes de datos de telemetría desde los dispositivos IoT en la nube para su almacenamiento o procesamiento. En esta guía de inicio rápido va a enviar datos de telemetría a una instancia de IoT Hub desde una aplicación Android que se ejecuta en un dispositivo físico o simulado.

La guía de inicio rápido usa una aplicación Android escrita anteriormente para enviar telemetría. Los datos de telemetría se leerán desde la instancia de IoT Hub con Azure Cloud Shell. Antes de ejecutar la aplicación, creará un centro de IoT Hub y registrará un dispositivo con el centro.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* Android Studio desde https://developer.android.com/studio/. Para más información relativa a la instalación de Android Studio, consulte [android-installation](https://developer.android.com/studio/install). 

* En el ejemplo de este artículo se usa Android SDK 27.

* Ejecute el siguiente comando para agregar la extensión IoT de Microsoft Azure para la CLI de Azure a la instancia de Cloud Shell. La extensión IOT agrega comandos específicos de IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) a la CLI de Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* La [aplicación Android de ejemplo](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) que va a ejecutar en inicio rápido es parte del repositorio azure-iot-samples-java en GitHub. Descargue o clone el repositorio [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar un dispositivo

Debe registrar un dispositivo con IoT Hub antes de poder conectarlo. En esta guía de inicio rápido, usará Azure Cloud Shell para registrar un dispositivo simulado.

1. Ejecute los siguientes comandos en Azure Cloud Shell para crear la identidad del dispositivo.

   **YourIoTHubName**: reemplace este marcador de posición por el nombre elegido para el centro de IoT.

   **MyAndroidDevice**: es el nombre que se da al dispositivo registrado. Use MyAndroidDevice como se muestra a continuación. Si elige otro nombre para el dispositivo, tendrá que usarlo en todo el artículo y actualizar el nombre del dispositivo en las aplicaciones de ejemplo antes de ejecutarlas.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Ejecute los siguientes comandos en Azure Cloud Shell para obtener la _cadena de conexión del dispositivo_ que acaba de registrar:

    **YourIoTHubName**: reemplace este marcador de posición por el nombre elegido para el centro de IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Anote la cadena de conexión del dispositivo, que se parecerá a esta:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Use este valor más adelante en esta guía de inicio rápido para enviar datos de telemetría.

## <a name="send-telemetry"></a>Envío de datos de telemetría

1. En Android Studio, abra el proyecto de Android de ejemplo de GitHub. El proyecto se encuentra en el siguiente directorio de la copia que ha clonado o descargado del repositorio [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. En Android Studio, abra *gradle.properties* para el proyecto de ejemplo y reemplace el marcador de posición **Device_Connection_String** por la cadena de conexión de dispositivo que anotó anteriormente.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. En Android Studio, haga clic en **File**(Archivo) > **Sync Project with Gradle Files** (Sincronizar proyecto con archivos de Gradle). Compruebe que la compilación se completa.

   > [!NOTE]
   > Si se produce un error en la sincronización del proyecto, puede ser por uno de los siguientes motivos:
   >
   > * Las versiones del complemento Gradle para Android y Gradle al que se hace referencia en el proyecto están en desuso para su versión de Android Studio. Siga [estas instrucciones](https://developer.android.com/studio/releases/gradle-plugin) para hacer referencia a las versiones correctas del complemento y de Gradle e instalarlas.
   > * No se ha firmado el contrato de licencia de Android SDK. Siga las instrucciones de la salida de compilación para firmar el contrato de licencia y descargar el SDK.

4. Una vez completada la compilación, haga clic en **Run**(Ejecutar) > **Run 'app'** (Ejecutar "aplicación"). Configure la aplicación para que se ejecute en un dispositivo Android físico o un emulador de Android. Para más información sobre la ejecución de una aplicación de Android en un dispositivo físico o en un emulador, consulte [Ejecutar la aplicación](https://developer.android.com/training/basics/firstapp/running-app).

5. Cuando se cargue la aplicación, haga clic en el botón **Iniciar** para empezar a enviar datos de telemetría a IoT Hub:

    ![Application](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Lectura de los datos de telemetría procedentes de su instancia de IoT Hub

En esta sección, usará Azure Cloud Shell con la [extensión de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para supervisar los mensajes de dispositivo que envía el dispositivo Android.

1. Mediante Azure Cloud Shell, ejecute el siguiente comando para conectarse y leer mensajes desde el centro de IoT:

   **YourIoTHubName**: reemplace este marcador de posición por el nombre elegido para el centro de IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    La siguiente captura de pantalla muestra la salida en la que IoT Hub recibe los datos de telemetría que el dispositivo Android ha enviado:

      ![Leer los mensajes de dispositivo mediante la CLI de Azure](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha configurado un centro de IoT, registrado un dispositivo, enviado datos de telemetría simulados al centro con una aplicación Android y leído datos de telemetría desde el centro con Azure Cloud Shell.

Para aprender a controlar el dispositivo simulado desde una aplicación back-end, vaya al siguiente inicio rápido.

> [!div class="nextstepaction"]
> [Inicio rápido: Control de un dispositivo conectado a IoT Hub](quickstart-control-device-android.md)

