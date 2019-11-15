---
title: Conexión del código del dispositivo de ejemplo de la versión preliminar de IoT Plug and Play a IoT Hub (Linux) | Microsoft Docs
description: Compile y ejecute el código de dispositivo de ejemplo de IoT Plug and Play en Linux que se conecta a un centro de IoT. Use la CLI de Azure para ver la información enviada por el dispositivo al centro.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 38a6deb8d021c5e6a20d765cc7aee5b86042f557
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585981"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub"></a>Inicio rápido: Conexión de una aplicación de dispositivo de ejemplo IoT Plug and Play en versión preliminar que se ejecuta en Linux con IoT Hub

En este inicio rápido se muestra cómo crear una aplicación de dispositivo IoT Plug and Play de ejemplo en Linux, conectarla a IoT Hub y usar la CLI de Azure para ver la información que envía al centro. La aplicación de ejemplo está escrita en C y se incluye en el SDK de dispositivo IoT de Azure para C. Los desarrolladores de soluciones pueden usar la CLI de Azure para conocer las funcionalidades de cualquier dispositivo IoT Plug and Play sin necesidad de ver nada de código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Requisitos previos

En este inicio rápido se asume que utiliza Ubuntu Linux. Los pasos de este tutorial se han probado con Ubuntu 18.04.

Para completar este inicio rápido, es preciso instalar el siguiente software en la máquina Linux local:

Instale **GCC**, **GIT**, **cmake** y todas las dependencias con el comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Compruebe que la versión de `cmake` es posterior a **2.8.12** y que la versión de **GCC** es posterior a **4.4.7**.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>Preparación de un centro de IoT

También necesitará un centro de IoT de Azure en la suscripción de Azure para completar este inicio rápido. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

> [!IMPORTANT]
> Durante la versión preliminar pública, las características de IoT Plug and Play solo están disponibles en los centros de IoT creados en las regiones **Centro de EE. UU.** , **Norte de Europa** y **Este de Japón**.

Si usa la CLI de Azure localmente, la versión de `az` debe ser la **2.0.73** o posterior. Azure Cloud Shell usa la versión más reciente. Use el comando `az --version` para comprobar la versión instalada en la máquina.

Agregue la extensión Microsoft Azure IoT para la CLI de Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

En los pasos de este de inicio rápido se requiere la versión **0.8.5** o posterior de la extensión. Use el comando `az extension list` para comprobar la versión que tiene instalada, y el comando `az extension update` para actualizar si es necesario.

Si usa la CLI localmente, inicie sesión en la suscripción a Azure con el siguiente comando:

```bash
az login
```

Si usa Azure Cloud Shell, ya ha iniciado sesión automáticamente.

Si aún no tiene IoT Hub, siga [estas instrucciones para crearlo](../iot-hub/iot-hub-create-using-cli.md): Durante la versión preliminar pública IoT Plug and Play está disponible en las regiones Norte de Europa, Centro de EE. UU. y Japón Oriental. Asegúrese de que crea el centro en una de estas regiones.

Ejecute el siguiente comando la nueva identidad del dispositivo en su centro de IoT. Reemplace el marcador de posición **YourIoTHubName** por el nombre que eligió para su instancia de IoT Hub.

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id mydevice
```

Ejecute los siguientes comandos para obtener la _cadena de conexión del dispositivo_ que acaba de registrar:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id mydevice --output table
```

## <a name="prepare-the-development-environment"></a>Preparación del entorno de desarrollo

En este inicio rápido se prepara un entorno de desarrollo que se puede usar para clonar y compilar el SDK de dispositivo IoT de Azure para C.

Abra el símbolo del sistema. Ejecute el siguiente comando para clonar el repositorio de GitHub del [SDK para C de Azure IoT](https://github.com/Azure/azure-iot-sdk-c):

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

El comando tarda varios minutos en completarse.

## <a name="build-the-code"></a>Compilación del código

Para compilar el código de ejemplo incluido, se usa el SDK del dispositivo. La aplicación que se compila simula un dispositivo que se conecta a un centro de IoT. La aplicación envía datos de telemetría y propiedades, y recibe comandos.

1. Cree un subdirectorio `cmake` en la carpeta raíz del SDK del dispositivo y vaya a esa carpeta:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Ejecute los siguientes comandos para compilar el SDK del dispositivo y el código auxiliar generado:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Actualizar el repositorio del modelo

Antes de ejecutar el ejemplo, agregue el modelo de funcionalidad del dispositivo y las definiciones de interfaz al repositorio del modelo de empresa:

1. Inicie sesión en el [portal de Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) con su cuenta profesional o educativa de Microsoft o con su identificador de asociado de Microsoft, si tiene.

1. Seleccione **Company repository** (Repositorio de la empresa) y, luego, **Capability models** (Modelos de funcionalidad).

1. Seleccione **Nuevo** y, a continuación, **Cargar**.

1. Seleccione el archivo `SampleDevice.capabilitymodel.json` en la carpeta `digitaltwin_client/samples` en la carpeta raíz del SDK del dispositivo. Seleccione **Abrir** y, después, **Guardar** para cargar el archivo de modelo en el repositorio.

1. Seleccione **Company repository** (Repositorio de la empresa) y, luego, **Interfaces**.

1. Seleccione **Nuevo** y, a continuación, **Cargar**.

1. Seleccione el archivo `EnvironmentalSensor.interface.json` en la carpeta `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` en la carpeta raíz del SDK del dispositivo. Seleccione **Abrir** y, después, **Guardar** para cargar el archivo de interfaz en el repositorio.

1. Seleccione **Company repository** (Repositorio de la empresa) y, luego, **Connection strings** (Cadenas de conexión). Tome nota de la primera cadena de conexión del repositorio del modelo de la empresa, ya que la usará más adelante en este inicio rápido.

## <a name="run-the-sample"></a>Ejecución del ejemplo

Ejecute una aplicación de ejemplo en el SDK para simular un dispositivo IoT Plug and Play que envía telemetría a su instancia de IoT Hub. Para ejecutar la aplicación de ejemplo:

1. Desde la carpeta `cmake`, navegue hasta la carpeta que contiene el archivo ejecutable:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Ejecute el archivo ejecutable:

    ```bash
    ./digitaltwin_sample_device "{your device connection string}"
    ```

El dispositivo simulado inicia el envío de telemetría, la escucha de comandos y la escucha de actualizaciones de propiedades.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Uso de la CLI de IoT de Azure para validar el código

Una vez iniciado el ejemplo de cliente de dispositivo, compruebe que funciona con la CLI de Azure.

Use el siguiente comando para ver la telemetría que envía el dispositivo de ejemplo. Es posible que tenga que esperar un minuto o dos antes de ver la telemetría en la salida:

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mydevice
```

Use el siguiente comando para ver las propiedades que envía el dispositivo:

```azurecli-interactive
az iot dt list-properties --hub-name {your IoT hub} --device-id mydevice --interface sensor --source private --repo-login "{your company model repository connection string}"
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a conectar un dispositivo IoT Plug and Play a un centro de IoT. Para más información sobre cómo crear una solución que interactúe con dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Procedimiento: Conexión e interacción con un dispositivo](howto-develop-solution.md)
