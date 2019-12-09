---
title: 'Inicio rápido: Reconocimiento de la voz a través de un micrófono en Java (Android): servicio de voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a reconocer la voz en Java para Android mediante el SDK de Voz
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: e492381c4ab2f7ab41b5363ed70f9c5c6b12ce99
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818992"
---
## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar:

> [!div class="checklist"]
> * [Ha creado un recurso de Voz de Azure](../../../../get-started.md)
> * [Ha configurado el entorno de desarrollo](../../../../quickstarts/setup-platform.md?tabs=android)
> * Asegúrese de que tiene acceso a un micrófono para capturar el audio.

## <a name="create-a-user-interface"></a>Creación de una interfaz de usuario

Ahora vamos a crear una interfaz de usuario básica para la aplicación. Edite el diseño de la actividad principal, `activity_main.xml`. Inicialmente, el diseño incluye una barra de título con el nombre de la aplicación y una vista de texto que contiene el texto "Hello World!" (Hola mundo).

* Seleccione el elemento TextView. Cambie su atributo ID de la esquina superior derecha por `hello`.

* En la paleta de la esquina superior izquierda de la ventana `activity_main.xml`, arrastre un botón al espacio vacío por encima del texto.

* En los atributos del botón de la derecha, en el valor del atributo `onClick`, escriba `onSpeechButtonClicked`. Vamos a escribir un método con este nombre para controlar el evento de botón. Cambie su atributo ID de la esquina superior derecha por `button`.

* Si quiere deducir las restricciones de diseño que se le aplican, use el icono de varita mágica de la parte superior del diseñador.

  ![Captura de pantalla del icono de varita mágica](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

El texto y la representación gráfica de la interfaz de usuario ahora deben tener este aspecto:

![Interfaz de usuario](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Incorporación de código de ejemplo

1. Abra el archivo de origen `MainActivity.java`. Reemplace todo el código de este archivo por lo siguiente:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * El método `onCreate` incluye código que solicita permisos para micrófono e Internet, y que inicializa el enlace de plataforma nativa. Solo hay que configurar los enlaces de plataforma nativa una vez. Debe realizarse al principio durante la inicialización de la aplicación.

   * El método `onSpeechButtonClicked` es, como se indicó anteriormente, el controlador de clic de botón. Al presionar un botón, se desencadena la transcripción de voz a texto.

1. En el mismo archivo, reemplace la cadena `YourSubscriptionKey` por la clave de suscripción.

1. Reemplace también la cadena `YourServiceRegion` por la [región](~/articles/cognitive-services/Speech-Service/regions.md) asociada a su suscripción. Por ejemplo, use `westus` para la suscripción de prueba gratuita.

## <a name="build-and-run-the-app"></a>Compilación y ejecución de la aplicación

1. Conecte el dispositivo Android al equipo de desarrollo. Asegúrese de que tiene [habilitado el modo de desarrollo y la depuración USB](https://developer.android.com/studio/debug/dev-options) en el dispositivo.

1. Para compilar la aplicación, seleccione Ctrl+F9 o elija **Build (Compilar)**  > **Make Project (Crear proyecto)** desde la barra de menús.

1. Para iniciar la aplicación, seleccione Mayús+F10 o elija **Run (Ejecutar)**  > **Run 'app' (Ejecutar "aplicación")** .

1. En la ventana de destino de implementación que aparece, seleccione el dispositivo Android.

   ![Captura de pantalla de la ventana Select Deployment Target (Seleccionar destino de implementación)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Seleccione el botón de la aplicación para empezar una sección de reconocimiento de voz. Los siguientes 15 segundos de la oración en inglés se enviarán al servicio de Voz y transcrito. El resultado aparece en la aplicación Android y en la ventana de logcat de Android Studio.

![Captura de pantalla de la aplicación Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [footer](./footer.md)]

