---
title: 'Configurar contenedores: Computer Vision'
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo configurar los valores obligatorios y opcionales para los contenedores de Reconocer texto en Computer Vision.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718994"
---
# <a name="configure-computer-vision-docker-containers"></a>Configuración de contenedores de Docker de Computer Vision

El entorno de ejecución del contenedor de Computer Vision se configura mediante los argumentos del comando `docker run`. Este contenedor tiene varias opciones de configuración necesarias, así como otras opcionales. Hay disponibles varios [ejemplos](#example-docker-run-commands) del comando. La configuración específica del contenedor es la configuración de facturación. 

## <a name="configuration-settings"></a>Valores de configuración

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Las opciones [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) y [`Eula`](#eula-setting) se usan en conjunto y debe proporcionar valores válidos para las tres; en caso contrario, no se inicia el contenedor. Para obtener más información sobre el uso de estas opciones de configuración para crear instancias de un contenedor, consulte [Facturación](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Opción de configuración ApiKey

La opción de configuración `ApiKey` especifica la clave de recurso de Azure `Cognitive Services` usada para realizar un seguimiento de la información de facturación del contenedor. Debe especificar un valor para ApiKey que debe ser una clave válida para el recurso de _Cognitive Services_ especificado para la opción de configuración [`Billing`](#billing-configuration-setting).

Este valor se puede encontrar en el siguiente lugar:

* Azure Portal: Administración de recursos de **Cognitivas Services**, en **Claves**.

## <a name="applicationinsights-setting"></a>Opción de configuración ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Opción de configuración Billing

La opción de configuración `Billing` especifica el URI del punto de conexión del recurso de _Cognitive Services_ que se usa para medir la información de facturación del contenedor. Debe especificar un valor para esta opción de configuración, que debe ser un URI de punto de conexión válido para un recurso de _Cognitive Services_ en Azure. El contenedor informa sobre el uso cada 10 a 15 minutos.

Este valor se puede encontrar en el siguiente lugar:

* Azure Portal: Información general de **Cognitivas Services**, con la etiqueta `Endpoint`

No olvide agregar la ruta `vision/v1.0` al URI de punto de conexión, tal como se muestra en la tabla siguiente. 

|Obligatorio| NOMBRE | Tipo de datos | DESCRIPCIÓN |
|--|------|-----------|-------------|
|Sí| `Billing` | Cadena | Identificador URI del punto de conexión de facturación<br><br>Ejemplo:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Opción de configuración Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Opción de configuración Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configuración de las credenciales del proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Opción de configuración Logging
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configuración de montaje

Utilice montajes de enlace para leer y escribir datos hacia y desde el contenedor. Puede especificar un montaje de entrada o un montaje de salida mediante la opción `--mount` del comando [docker run](https://docs.docker.com/engine/reference/commandline/run/).

El contenedor de Computer Vision no usa los montajes de entrada o salida para almacenar datos de entrenamiento o del servicio. 

La sintaxis exacta de la ubicación de montaje del host varía según el sistema operativo del host. Además, la ubicación de montaje del [equipo host](computer-vision-how-to-install-containers.md#the-host-computer) puede no ser accesible debido a un conflicto entre los permisos que usa la cuenta de servicio de Docker y los permisos de la ubicación de montaje del host. 

|Opcional| NOMBRE | Tipo de datos | DESCRIPCIÓN |
|-------|------|-----------|-------------|
|No permitida| `Input` | Cadena | Los contenedores de Computer Vision no usan esto.|
|Opcional| `Output` | Cadena | Destino del montaje de salida. El valor predeterminado es `/output`. Esta es la ubicación de los registros. Esto incluye los registros de contenedor. <br><br>Ejemplo:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandos de ejemplo de docker run

Los ejemplos siguientes usan las opciones de configuración para ilustrar cómo escribir y usar comandos `docker run`.  Una vez que se está ejecutando, el contenedor continúa ejecutándose hasta que lo [detenga](computer-vision-how-to-install-containers.md#stop-the-container).

* **Carácter de continuación de línea**: Los comandos de Docker de las secciones siguientes usan la barra diagonal inversa (`\`) como un carácter de continuación de línea. Puede quitarla o reemplazarla en función de los requisitos del sistema operativo del host. 
* **Orden de los argumentos**: No cambie el orden de los argumentos a menos que esté muy familiarizado con los contenedores de Docker.

Reemplace {_argument_name_} por sus propios valores:

| Marcador de posición | Valor | Formato o ejemplo |
|-------------|-------|---|
| **{CLAVE_API}** | La clave del punto de conexión del recurso `Computer Vision` en la página Claves de `Computer Vision` de Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{URI_PUNTODECONEXIÓN}** | El valor del punto de conexión de facturación está disponible en la página Información general de Azure `Computer Vision`.| Consulte el apartado de [recopilación de los parámetros necesarios](computer-vision-how-to-install-containers.md#gathering-required-parameters) para ejemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Para poder ejecutar el contenedor, las opciones `Eula`, `Billing` y `ApiKey` deben estar especificadas; de lo contrario, el contenedor no se iniciará.  Para obtener más información, vea [Facturación](computer-vision-how-to-install-containers.md#billing).
> El valor de ApiKey es la **clave** de la página de claves de recursos de Azure `Cognitive Services`.

## <a name="container-docker-examples"></a>Ejemplos de contenedor de Docker

Los siguientes ejemplos de Docker son del contenedor Lectura.

### <a name="basic-example"></a>Ejemplo básico

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Ejemplo de registro 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Instalación y ejecución de contenedores](computer-vision-how-to-install-containers.md).
