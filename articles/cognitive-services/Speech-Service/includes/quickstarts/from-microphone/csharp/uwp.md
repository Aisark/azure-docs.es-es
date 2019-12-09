---
title: 'Inicio rápido: Reconocimiento de la voz a través de un micrófono en C# (UWP): servicio de voz'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 5f83f249228c96021979edc14c092eded03b96e4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818658"
---
## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar:

> [!div class="checklist"]
> * [Ha creado un recurso de Voz de Azure](../../../../get-started.md)
> * [Ha configurado el entorno de desarrollo](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Ha creado un proyecto de ejemplo vacío](../../../../quickstarts/create-project.md?tabs=uwp)
> * Asegúrese de que tiene acceso a un micrófono para capturar el audio.

Si ya lo ha hecho, perfecto. Vamos a continuar.

## <a name="open-your-project-in-visual-studio"></a>Abra el proyecto en Visual Studio.

El primer paso es asegurarse de que tiene el proyecto abierto en Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Inicio con código reutilizable

Vamos a agregar código que funcione como el esqueleto del proyecto.

1. En el **Explorador de soluciones**, abra `MainPage.xaml`.

2. En la vista XAML del diseñador, inserte el fragmento de código XAML siguiente en la etiqueta **Cuadrícula** (entre `<Grid>` y `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. En **Explorador de soluciones**, abra el archivo de código fuente subyacente `MainPage.xaml.cs`. (Se agrupa en `MainPage.xaml`).

4. Reemplace el código por el siguiente código base:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Creación de una configuración de Voz

Antes de inicializar un objeto `SpeechRecognizer`, debe crear una configuración que use la clave y la región de suscripción. Inserte este código en el método `RecognizeSpeechAsync()`.

> [!NOTE]
> En este ejemplo se usa el método `FromSubscription()` para compilar la clase `SpeechConfig`. Para ver una lista completa de los métodos disponibles, consulte [Clase SpeechConfig](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)].

## <a name="initialize-a-speechrecognizer"></a>Inicialización de un objeto SpeechRecognizer

Ahora, vamos a crear un objeto `SpeechRecognizer`. Este objeto se crea dentro de una instrucción using para garantizar la versión correcta de los recursos no administrados. Inserte este código en el método `RecognizeSpeechAsync()`, justo debajo de la configuración de Voz.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Reconocimiento de una frase

En el objeto `SpeechRecognizer`, va a llamar al método `RecognizeOnceAsync()`. Este método permite que el servicio Voz sepa que solo va a enviar una frase para el reconocimiento y que, una vez que se identifica la frase, se detendrá el reconocimiento de voz.

Dentro de la instrucción using, agregue este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Visualización de los resultados (o errores) del reconocimiento

Cuando el servicio Voz devuelva el resultado del reconocimiento, querrá hacer algo con él. Vamos a hacer algo tan sencillo como imprimir el resultado en el panel de estado.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Compilación y ejecución de la aplicación

Ahora está listo para compilar y probar la aplicación.

1. En la barra de menús, elija **Compilar** > **Compilar solución** para compilar la aplicación. El código se debería compilar sin errores ahora.

1. Elija **Depurar** > **Iniciar depuración** o presione **F5** para iniciar la aplicación. Aparece la ventana **HelloWorld**.

   ![Ejemplo de aplicación de reconocimiento de voz de UWP en C#: inicio rápido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Seleccione **Habilitar micrófono** y, cuando aparezca la solicitud de permiso de acceso, seleccione **Sí**.

   ![Solicitud de permiso de acceso al micrófono](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Seleccione **Speech recognition with microphone input** (Reconocimiento de voz con entrada de micrófono) y diga una frase en inglés en el micrófono del dispositivo. Lo que diga se transmitirá al servicio de Voz y se transcribe en texto, que aparece en la misma ventana.

   ![Interfaz de usuario del reconocimiento de voz](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [footer](./footer.md)]
