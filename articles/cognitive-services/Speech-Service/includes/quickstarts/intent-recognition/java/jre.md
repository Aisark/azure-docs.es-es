---
title: 'Inicio rápido: Reconocimiento de voz, intenciones y entidades en Java: servicio de voz'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/02/2020
ms.topic: include
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 7654d952469e35c3b45af252e910cb0e36693029
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772841"
---
## <a name="prerequisites"></a>Prerequisites

Antes de comenzar:

* Si este es su primer proyecto en Java (JRE), use esta guía para <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=jre" target="_blank">crear un proyecto de ejemplo vacío</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">Instale el SDK de Voz de su entorno de desarrollo</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Creación de una aplicación de LUIS para el reconocimiento de la intención

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Apertura del proyecto

1. Abra el entorno de desarrollo integrado que prefiera.
2. Cargue el proyecto y abra `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Inicio con código reutilizable

Vamos a agregar código que funcione como el esqueleto del proyecto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Creación de una configuración de Voz

Para poder inicializar un objeto `IntentRecognizer`, es preciso crear una configuración que use la clave y ubicación del recurso de predicción de LUIS.  

Inserte este código en el bloque try/catch en `main()`. Asegúrese de actualizar estos valores:

* Reemplace `"YourLanguageUnderstandingSubscriptionKey"` por la clave de predicción de LUIS. 
* Reemplace `"YourLanguageUnderstandingServiceRegion"` por la ubicación de LUIS. 

>[!TIP]
> Si necesita ayuda para encontrar estos valores, consulte [Creación de una aplicación de LUIS para el reconocimiento de la intención](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

En este ejemplo se usa el método `FromSubscription()` para compilar la clase `SpeechConfig`. Para ver una lista completa de los métodos disponibles, consulte [Clase SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

El SDK de Voz se usará de forma predeterminada para reconocer el uso de en-us como idioma. Para más información sobre cómo elegir el idioma de origen, consulte [Especificación del idioma de origen para la conversión de voz a texto](../../../../how-to-specify-source-language.md).

## <a name="initialize-an-intentrecognizer"></a>Inicialización de IntentRecognizer

Ahora, vamos a crear un objeto `IntentRecognizer`. Inserte este código justo debajo de la configuración de Voz.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adición de un objeto LanguageUnderstandingModel e intenciones

Debe asociar un objeto `LanguageUnderstandingModel` con el reconocedor de intenciones y agregar las intenciones que desee que se reconozcan. Vamos a usar las intenciones del dominio precompilado para la automatización doméstica. 

Inserte este código debajo de `IntentRecognizer`. Asegúrese de reemplazar `"YourLanguageUnderstandingAppId"` por el identificador de la aplicación de LUIS. 

>[!TIP]
> Si necesita ayuda para encontrar este valor, consulte [Creación de una aplicación de LUIS para el reconocimiento de la intención](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Reconocimiento de una intención

En el objeto `IntentRecognizer`, va a llamar al método `recognizeOnceAsync()`. Este método permite que el servicio Voz sepa que solo va a enviar una frase para el reconocimiento y que, una vez que se identifica la frase, se detendrá el reconocimiento de voz.

Inserte este código debajo del modelo:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Visualización de los resultados (o errores) del reconocimiento

Cuando el servicio Voz devuelva el resultado del reconocimiento, querrá hacer algo con él. Vamos a hacer algo tan sencillo como imprimir el resultado en la consola.

Inserte este código debajo de la llamada a `recognizeOnceAsync()`: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Liberación de recursos

Es importante liberar los recursos de voz cuando haya terminado de usarlos. Inserte este código al final del bloque try/catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Comprobación del código

En este momento, el código debe tener esta apariencia:  

> [!NOTE]
> Se han agregado algunos comentarios a esta versión.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Compilación y ejecución de la aplicación

Presione F11, o seleccione **Run (Ejecutar)**  > **Debug (Depurar)** .
Los próximos 15 segundos de la entrada de voz del micrófono se reconocen y se registran en la ventana de consola.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [footer](./footer.md)]