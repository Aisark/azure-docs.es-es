---
title: Implementación de la puerta de enlace de factoría conectada (Azure) | Microsoft Docs
description: Cómo implementar una puerta de enlace en Windows o Linux para permitir la conectividad al acelerador de la solución de factoría conectada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182105"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Implementación de una puerta de enlace de perímetro en Windows o Linux para el acelerador de la solución de factoría conectada

Para implementar una puerta de enlace de perímetro para el acelerador de la solución de *factoría conectada*, se necesitan dos componentes de software:

-  *OPC Proxy*  establece una conexión a la factoría conectada. El componente OPC Proxy espera los mensajes de comando y control del explorador de OPC integrado que se ejecuta en el portal de la solución de factoría conectada.

-  *OPC Publisher*  se conecta a los servidores locales existentes de agente de usuario de OPC y reenvía sus mensajes de telemetría a la factoría conectada. Puede conectar un dispositivo clásico OPC mediante el [adaptador clásico de OPC para OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Ambos componentes son de código abierto y están disponibles como código fuente en GitHub y como contenedores de Docker en DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

No se requiere ninguna dirección IP de acceso público ni puertos de entrada abiertos en el firewall de la puerta de enlace para ninguno de los componentes. Los componentes OPC Proxy y OPC Publisher solo usan el puerto de salida 443.

Los pasos que se indican en este artículo muestran cómo implementar una puerta de enlace de perímetro con Docker en Windows o Linux. La puerta de enlace permite la conectividad con el acelerador de la solución de factoría conectada. Aunque también puede usar los componentes sin ella.

> [!NOTE]
> Ambos componentes se pueden usar como módulos en  [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Selección de un dispositivo de puerta de enlace

Si todavía no tiene un dispositivo de puerta de enlace, Microsoft le recomienda comprar una puerta de enlace comercial de uno de nuestros asociados. Visite el  [catálogo de dispositivos de Azure IoT](https://catalog.azureiotsolutions.com/?q=opc) para ver una lista de los dispositivos de puerta de enlace compatibles con la solución de factoría conectada. Siga las instrucciones que se incluyen con el dispositivo para configurar la puerta de enlace.

Use las instrucciones siguientes si necesita configurar manualmente un dispositivo de puerta de enlace existente.

## <a name="install-and-configure-docker"></a>Instalación y configuración de Docker

Instale  [Docker para Windows](https://www.docker.com/docker-windows)  en el dispositivo de puerta de enlace basado en Windows o use un administrador de paquetes para instalar Docker en el dispositivo de puerta de enlace basado en Linux.

Durante la configuración de Docker para Windows, seleccione una unidad en la máquina host para compartir con Docker. En la captura de pantalla siguiente se muestra el uso compartido de la unidad **D** en el sistema Windows. Compartir una unidad permite el acceso a la unidad host desde dentro de un contenedor de docker:

![Instalación de Docker para Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> También puede realizar este paso después de instalar docker desde el cuadro de diálogo  **Configuración** . Haga clic con el botón derecho en el icono de **Docker** en la bandeja del sistema de Windows y elija **Configuración**. Si se han implementado las actualizaciones principales de Windows en el sistema, como la actualización Windows Fall Creators, deje de compartir las unidades y compártalas de nuevo para actualizar los derechos de acceso.

Si usa Linux, no es necesaria ninguna configuración adicional para permitir el acceso al sistema de archivos.

En Windows, cree una carpeta en la unidad compartida con Docker; en Linux, cree una carpeta en el sistema de archivos raíz. En este tutorial se hace referencia a esta carpeta como `<SharedFolder>`.

Cuando haga referencia a la carpeta `<SharedFolder>` en un comando de Docker, asegúrese de usar la sintaxis correcta correspondiente a su sistema operativo. A continuación se muestran dos ejemplos, uno para Windows y otro para Linux:

- Si usa la carpeta `D:\shared` en Windows como `<SharedFolder>`, la sintaxis de comandos de Docker será `d:/shared`.

- Si usa la carpeta `/shared` en Linux como `<SharedFolder>`, la sintaxis de comandos de Docker será `/shared`.

Para más información, consulte la referencia del motor de Docker [Use volumes](https://docs.docker.com/engine/admin/volumes/volumes/) (Uso de volúmenes).

## <a name="configure-the-opc-components"></a>Configuración de los componentes OPC

Antes de instalar los componentes OPC, realice los pasos siguientes para preparar su entorno:

1. Para realizar la implementación de la puerta de enlace, necesitará la cadena de conexión  **iothubowner**  de IoT Hub en la implementación de factoría conectada. En  [Azure Portal](https://portal.azure.com/), vaya a su instancia de IoT Hub en el grupo de recursos que creó cuando implementó la solución de factoría conectada. Haga clic en  **Directivas de acceso compartido** para acceder a la cadena de conexión  **iothubowner** :

    ![Búsqueda de la cadena de conexión IoT Hub](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Copie el valor de  **Cadena de conexión: clave principal** .

1. Para permitir la comunicación entre contenedores de Docker, se necesita una red de puente definida por el usuario. Para crear una red de puente para los contenedores, ejecute los siguientes comandos en un símbolo del sistema:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Para comprobar que se ha creado la red de puente **iot_edge**, ejecute el siguiente comando:

    ```cmd/sh
    docker network ls
    ```

    Su red de puente **iot_edge** se incluye en la lista de redes.

Para ejecutar OPC Publisher, ejecute el siguiente comando en un símbolo del sistema:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- En el [repositorio de GitHub de OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) y en la [referencia de ejecución de Docker](https://docs.docker.com/engine/reference/run/) se proporciona más información sobre:

  - Las opciones de línea de comandos de Docker especificadas delante del nombre de contenedor (`microsoft/iot-edge-opc-publisher:2.1.4`).
  - El significado de los parámetros de línea de comandos de OPC Publisher especificados después del nombre de contenedor (`microsoft/iot-edge-opc-publisher:2.1.4`).

- `<IoTHubOwnerConnectionString>` es la cadena de conexión de la directiva de acceso compartido **iothubowner** de Azure Portal. Esta cadena la copió en un paso anterior. Solo es necesaria la primera vez que se ejecuta OPC Publisher. En las ejecuciones posteriores se debe omitir porque supone un riesgo para la seguridad.

- La carpeta `<SharedFolder>` usada y su sintaxis se describen en la sección [Instalación y configuración de Docker](#install-and-configure-docker). OPC Publisher usa `<SharedFolder>` para:

    - Leer y escribir en el archivo de configuración de OPC Publisher.
    - Escribir en el archivo de registro.
    - Hacer que ambos archivos estén disponibles fuera del contenedor.

- OPC Publisher lee su configuración desde el archivo **publishednodes.json**, que se lee desde la carpeta `<SharedFolder>/docker` y se escribe en ella. En este archivo de configuración se definen los datos del nodo OPC UA en un servidor OPC UA dado al que debe suscribirse OPC Publisher. La sintaxis completa del archivo **publishednodes.json** se describe en la página [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) en GitHub. Al agregar una puerta de enlace, coloque un archivo **publishednodes.json** vacío en la carpeta:

    ```json
    [
    ]
    ```

- Cada vez que el servidor OPC UA notifica a OPC Publisher un cambio de datos, el nuevo valor se envía a IoT Hub. En función de la configuración de procesamiento por lotes, OPC Publisher podría acumular primero los datos antes de enviarlos a IoT Hub en un lote.

- Docker no admite la resolución de nombres NetBIOS, solo la resolución de nombres DNS. Si no tiene un servidor DNS en la red, puede usar la solución alternativa que se muestra en el ejemplo anterior de línea de comandos. En el ejemplo anterior de línea de comandos se usa el parámetro `--add-host` para agregar una entrada en el archivo de hosts de contenedores. Esta entrada permite la búsqueda del nombre de host del valor de `<OpcServerHostname>` dado y resolver la dirección IP proporcionada `<IpAddressOfOpcServerHostname>`.

- OPC UA usa certificados X.509 para la autenticación y el cifrado. Coloque el certificado de OPC Publisher en el servidor OPC UA al que se va a conectar, para garantizar que se confía en OPC Publisher. El almacén de certificados de OPC Publisher está situado en la carpeta `<SharedFolder>/CertificateStores`. Puede encontrar el certificado de OPC Publisher en la carpeta `trusted/certs` de la carpeta `CertificateStores`.

  Los pasos para configurar el servidor OPC UA dependen del dispositivo que use. Normalmente, estos pasos se documentan en el manual del usuario del servidor OPC UA.

Para instalar OPC Proxy, ejecute el siguiente comando en un símbolo del sistema:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Solo debe ejecutar la instalación una vez en un sistema.

Use el siguiente comando para ejecutar OPC Proxy:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

OPC Proxy guarda la cadena de conexión durante la instalación. En las ejecuciones posteriores, se debe omitir la cadena de conexión ya que representa un riesgo para la seguridad.

## <a name="enable-your-gateway"></a>Habilitación de la puerta de enlace

Realice los pasos siguientes para habilitar la puerta de enlace del acelerador de la solución de factoría conectada:

1. Cuando ambos componentes estén en ejecución, vaya a la página  **Conectar el propio servidor OPC UA** del portal de la solución de factoría conectada. Esta página solo está disponible para los administradores de la solución. Escriba la dirección URL del punto de conexión de publicador (opc.tcp://publisher:62222) y haga clic en  **Conectar**.

1. Establezca una relación de confianza entre el portal de factoría conectada y OPC Publisher. Cuando vea una advertencia de certificado, haga clic en  **Continuar**. A continuación, verá un error que indica que OPC Publisher no confía en el cliente web de UA. Para resolver este error, copie el certificado de  **cliente web de UA** de la carpeta `<SharedFolder>/CertificateStores/rejected/certs` a la carpeta `<SharedFolder>/CertificateStores/trusted/certs` en la puerta de enlace. No necesita reiniciar la puerta de enlace.

Ahora puede conectarse a la puerta de enlace desde la nube y está preparado para agregar servidores OPC UA a la solución.

## <a name="add-your-own-opc-ua-servers"></a>Adición de los propios servidores OPC UA

Para agregar sus propios servidores OPC UA al acelerador de la solución de factoría conectada, siga estos pasos:

1. Vaya a la página  **Conectar el propio servidor OPC UA** del portal de la solución de factoría conectada.

    1. Inicie el servidor OPC UA al que desea conectarse. Asegúrese de que el servidor OPC UA es accesible desde las instancias de OPC Publisher y OPC Proxy que se ejecutan en el contenedor. Consulte los comentarios anteriores sobre la resolución de nombres.
    1. Escriba la dirección URL del punto de conexión del servidor OPC UA (`opc.tcp://<host>:<port>`) y haga clic en **Conectar**.
    1. El proceso de configuración de la conexión establece una relación de confianza entre el portal de factoría conectada (cliente OPC UA) y el servidor OPC UA al que está intentando conectarse. En el panel de factoría conectada aparece una advertencia que informa de que **no se puede comprobar el certificado del servidor al que desea conectarse**. Cuando vea una advertencia de certificado, haga clic en **Continuar**.
    1. Más difíciles de configurar son los parámetros del certificado del servidor OPC UA al que está intentando conectarse. En el caso de los servidores OPC UA basados en PC, quizá obtenga solo un cuadro de diálogo de advertencia en el panel que puede confirmar. En el caso de los sistemas de servidores OPC UA integrados, consulte la documentación de su servidor OPC UA para encontrar el modo de realizar esta tarea. Quizá necesite para ello el certificado del cliente de OPC UA del portal de factoría conectada. Un administrador puede descargar este certificado en la página **Connect your own OPC UA server** (Conecte su propio servidor OPC UA):

        ![Portal de solución](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Busque el árbol de nodos OPC UA del servidor OPC UA, haga clic con el botón derecho en los nodos OPC del que quiere enviar a la factoría conectada y seleccione  **Publicar**.

1. Ahora la telemetría fluye desde el dispositivo de puerta de enlace. Puede ver la telemetría en la vista  **Ubicaciones de factoría** del portal de factoría conectada en  **Nueva fábrica**.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la arquitectura del acelerador de la solución de factoría conectada, consulte el  [tutorial sobre el acelerador de la solución de factoría conectada](iot-accelerators-connected-factory-sample-walkthrough.md).

Aprenda sobre la  [implementación de referencia de OPC Publisher](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).