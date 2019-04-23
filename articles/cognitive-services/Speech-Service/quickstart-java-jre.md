---
title: 'Inicio rápido: Reconocimiento de voz, Java (Windows, Linux): servicios de Voz'
titleSuffix: Azure Cognitive Services
description: En este tutorial, aprenderá a crear una aplicación Java sencilla que captura y transcribe la voz del usuario por el micrófono del equipo.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: fe565d63e72b5ec2798dde03ba4f4bd9ff4f48a7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009406"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Inicio rápido: Reconocimiento de voz con el SDK de Voz para Java

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

En este artículo creará una aplicación de consola Java mediante el [SDK de Voz](speech-sdk.md). Transcribirá la conversión de voz en texto en tiempo real desde el micrófono de su PC. La aplicación se crea con el paquete Maven del SDK de Voz y el IDE de Java de Eclipse (v4.8) en Windows de 64 bits, Ubuntu Linux 16.04/18.04 de 64 bits o en macOS 10.13 o posterior. Se ejecuta en un entorno de tiempo de ejecución de Java 8 (JRE) de 64 bits.

> [!NOTE]
> Para el SDK de dispositivos de Voz y el dispositivo Roobo, consulte [SDK de dispositivos de voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Requisitos previos

Esta guía de inicio rápido requiere:

* Sistema operativo: Windows (64 bits), Ubuntu Linux 16.04/18.04 (64 bits) o macOS 10.13 o superior
* [IDE de Java de Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Una clave de suscripción de Azure para el servicio Voz. [Obtenga una gratis](get-started.md).

Si ejecuta Ubuntu 16.04/18.04, asegúrese de que estén instaladas estas dependencias antes de iniciar Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Si está ejecutando Windows (64 bits), asegúrese de que ha instalado Microsoft Visual C++ Redistributable para su plataforma.
* [Descarga de Microsoft Visual C++ Redistributable para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)


## <a name="create-and-configure-project"></a>Creación y configuración de un proyecto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Incorporación de código de ejemplo

1. Para agregar una nueva clase vacía al proyecto de Java, seleccione **File (Archivo)**  >  **New (Nuevo)**  >  **Class (Clase)**.

1. En la ventana **New Java Class** (Nueva clase de Java) escriba **speechsdk.quickstart** en el campo **Package** (Paquete) y **Main** en el campo **Name** (Nombre).

   ![Captura de pantalla de la ventana New Java Class (Nueva clase de Java)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Reemplace el código en `Main.java` con el siguiente fragmento de código:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Reemplace la cadena `YourSubscriptionKey` por la clave de suscripción.

1. Reemplace la cadena `YourServiceRegion` por la [región](regions.md) asociada con la suscripción (por ejemplo, `westus` para la suscripción de evaluación gratuita).

1. Guarde los cambios en el proyecto.

## <a name="build-and-run-the-app"></a>Compilación y ejecución de la aplicación

Presione F11, o seleccione **Run (Ejecutar)** > **Debug (Depurar)**.
Los próximos 15 segundos de la entrada de voz del micrófono se reconocen y se registran en la ventana de consola.

![Captura de pantalla de la salida de la consola después de un reconocimiento correcto](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Pasos siguientes

Se pueden encontrar ejemplos adicionales, por ejemplo, cómo leer voz de un archivo de audio, en GitHub.

> [!div class="nextstepaction"]
> [Exploración de ejemplos de Java en GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Otras referencias

- [Inicio rápido: Conversión de voz, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalización de modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalización de modelos de lenguaje](how-to-customize-language-model.md)
