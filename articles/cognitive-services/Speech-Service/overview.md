---
title: ¿Qué es Speech Service?
titleSuffix: Azure Cognitive Services
description: El Servicio de voz es la unificación de las funcionalidades de conversión de voz a texto, conversión de texto a voz y traducción de voz en una sola suscripción de Azure. Es fácil agregar voz a sus aplicaciones, herramientas y dispositivos con el SDK de Voz, el SDK de dispositivos de voz o las API de REST.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/05/2019
ms.author: erhopf
ms.openlocfilehash: df0ce5efefe86ed3b7e2e8ccf5a2bd9011e168e6
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261439"
---
# <a name="what-is-the-speech-service"></a>¿Qué es Speech Service?

El servicio de voz es la unificación de las funcionalidades de conversión de voz a texto, conversión de texto a voz y traducción de voz en una sola suscripción de Azure. Es fácil habilitar voz en sus aplicaciones, herramientas y dispositivos con el [SDK de voz](speech-sdk-reference.md), el [SDK de dispositivos de voz](https://aka.ms/sdsdk-quickstart) o las [API de REST](rest-apis.md).

> [!IMPORTANT]
> El Servicio de voz ha reemplazado a Bing Speech API, Translator Speech y Custom Speech. Consulte _Guías de procedimientos > Migración_ para obtener instrucciones de migración.

Estas características conforman el Servicio de voz. Use los vínculos en esta tabla para obtener más información sobre los casos de uso comunes para cada característica o examinar la referencia de API.

| Servicio | Característica | Descripción | SDK | REST |
| ------- | ------- | ----------- | --- | ---- |
| [Voz a texto](speech-to-text.md) | Voz a texto | Voz a texto transcribe secuencias de audio a texto en tiempo real que sus aplicaciones, herramientas o dispositivos pueden usar o mostrar. Use voz a texto con [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar las intenciones del usuario a partir de voz transcrita y actuar en los comandos de voz. | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch Transcription](batch-transcription.md) (Transcripción de Azure Batch) | La transcripción de Azure Batch permite la transcripción de voz a texto asincrónica de grandes volúmenes de datos. Este es un servicio basado en REST, que usa el mismo punto de conexión que la personalización y la administración de modelos. | No | [Sí](https://westus.cris.ai/swagger/ui/index) |
| | [Conversación entre varios dispositivos](multi-device-conversation.md) | Conexión de varios dispositivos o clientes en una conversación para enviar mensajes basados en voz o texto, con compatibilidad sencilla con transcripción y traducción| Sí | No |
| | [Transcripción de conversaciones](conversation-transcription-service.md) | Permite el reconocimiento de voz en tiempo real, la identificación del hablante y la diarización. Es perfecto para transcribir reuniones en persona con la capacidad de distinguir a los oradores. | Sí | No |
| | [Creación de modelos de voz personalizados](#customize-your-speech-experience) | Si usa voz a texto para el reconocimiento y la transcripción en un entorno único, puede crear y entrenar modelos acústicos, de lenguaje y pronunciación personalizados para dirigir el sonido ambiental o vocabulario específico del sector. | No | [Sí](https://westus.cris.ai/swagger/ui/index) |
| [Texto a voz](text-to-speech.md) | Texto a voz | Texto a voz convierte el texto de entrada en voz sintetizada similar a la humana mediante el [Lenguaje de marcado de síntesis de voz (SSML)](speech-synthesis-markup.md). Elija entre voces estándar y voces neuronales (consulte [Compatibilidad de idioma](language-support.md)). | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Creación de voces personalizadas](#customize-your-speech-experience) | Cree fuentes de voz personalizadas únicas para su marca o producto. | No | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traducción de voz](speech-translation.md) | Traducción de voz | La traducción de voz habilita la traducción de voz en varios idiomas en tiempo real en sus aplicaciones, herramientas y dispositivos. Use este servicio para la traducción de voz a voz y voz a texto. | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | No |
| [Asistentes de voz](voice-assistants.md) | Asistentes de voz | Los asistentes de voz que utilizan el Servicio de voz permiten a los desarrolladores crear interfaces de conversación naturales, similares a la humana, para sus aplicaciones y experiencias. El servicio del asistente de voz proporciona una interacción rápida y confiable entre un dispositivo y una implementación de asistente que usa el canal de voz de Direct Line Speech de Bot Framework o el servicio integrado de comandos personalizados (versión preliminar) para la finalización de tareas. | [Sí](voice-assistants.md) | No |

## <a name="news-and-updates"></a>Noticias y actualizaciones

Obtenga información sobre las novedades del Servicio de voz.

- Noviembre de 2019
  - Se han agregado dos nuevos estilos de habla, [`newscast`](speech-synthesis-markup.md#adjust-speaking-styles) y [`customerservice`](speech-synthesis-markup.md#adjust-speaking-styles) para la voz `en-US-JessaNeural`.
- Septiembre de 2019
  - SDK de Voz versión 1.7.0 publicado. Para obtener una lista completa de actualizaciones, mejoras y problemas conocidos, consulte las [Notas de la versión](releasenotes.md).
- Agosto de 2019
  - **Nuevo tutorial**: [Habilitar el bot con Voz mediante Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  - Se ha agregado un nuevo estilo de habla, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles), para la voz `en-US-JessaNeural`.
- Junio de 2019
  - El SDK de Voz versión 1.6.0 publicado. Para obtener una lista completa de actualizaciones, mejoras y problemas conocidos, consulte las [Notas de la versión](releasenotes.md).
- Mayo de 2019: ya hay documentación disponible para [Transcripción de conversaciones](conversation-transcription-service.md), [Transcripción de centros de llamadas](call-center-transcription.md) y [Asistentes de voz](voice-assistants.md).
- Mayo de 2019
  - Speech SDK versión 1.5.1 publicado. Para obtener una lista completa de actualizaciones, mejoras y problemas conocidos, consulte las [Notas de la versión](releasenotes.md).
  - Speech SDK versión 1.5.0 publicado. Para obtener una lista completa de actualizaciones, mejoras y problemas conocidos, consulte las [Notas de la versión](releasenotes.md).

## <a name="try-the-speech-service"></a>Prueba del Servicio de voz

Ofrecemos guías de inicio rápido en los lenguajes de programación más populares, cuyo diseño individual le permite ejecutar código en menos de 10 minutos. En esta tabla se incluyen las guías de inicio rápido más populares para cada característica. Use el menú de navegación izquierdo para explorar lenguajes y plataformas adicionales.

| Voz a texto (SDK) | Texto a voz (SDK) | Traducción (SDK) |
| -------------------- | -------------------- | ----------------- |
| [Reconocimiento de voz de un archivo de audio](quickstarts/speech-to-text-from-file.md) | [Síntesis de voz en un archivo de audio](quickstarts/text-to-speech-audio-file.md) | [Traducción de voz a texto](quickstarts/translate-speech-to-text.md) |
| [Reconocimiento de voz con un micrófono](quickstarts/speech-to-text-from-microphone.md) | [Síntesis de voz en un altavoz](quickstarts/text-to-speech.md) | [Traducción de voz a varios idiomas de destino](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Reconocimiento de voz almacenada en Blob Storage](quickstarts/from-blob.md) | [Síntesis asincrónica para audio de formato largo](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Traducción de voz a voz](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Voz a texto y texto a voz también tienen asociados puntos de conexión REST e inicios rápidos.

Una vez que haya tenido la oportunidad de usar el servicio de voz, pruebe nuestro tutorial, que le enseña a reconocer intenciones a partir de contenido de voz mediante el SDK de voz y LUIS.

- [Tutorial: Reconocimiento de intenciones a partir de contenido de voz con el SDK de voz y LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: Habilitación del bot con voz mediante el SDK de voz, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutorial: Compilación de una aplicación de Flask para traducir texto, analizar opiniones y sintetizar la voz de texto a voz, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obtención de código de ejemplo

Hay código de ejemplo para el Servicio de voz disponible en GitHub. En estos ejemplos se tratan escenarios comunes como la lectura de audio de un archivo o flujo, el reconocimiento continuo y de una sola emisión, y el trabajo con modelos personalizados. Use estos vínculos para ver ejemplos de SDK y REST:

- [Ejemplos de conversión de voz a texto, texto a voz y traducción de voz (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ejemplos de transcripción de Azure Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ejemplos de texto a voz (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Ejemplos del asistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalización de su experiencia de voz

El Servicio de voz funciona bien con los modelos integrados; sin embargo, es posible que desee personalizar y optimizar más la experiencia para su producto o entorno. Las opciones de personalización abarcan desde la optimización de modelos acústicos a fuentes de voz únicas para su marca.

| Speech Service | Plataforma | Descripción |
| -------------- | -------- | ----------- |
| Voz a texto | [Custom Speech](https://aka.ms/customspeech) | El reconocimiento de voz personalizado se adapta a sus necesidades y datos disponibles. Elimine las barreras del reconocimiento de voz, como el estilo de habla, el vocabulario y el ruido de fondo. |
| Text-to-Speech | [Voz personalizada](https://aka.ms/customvoice) | Cree una voz reconocible única para las aplicaciones de texto a voz con los datos de habla disponibles. Puede optimizar aún más las salidas de voz ajustando un conjunto de parámetros de voz. |

## <a name="reference-docs"></a>Documentos de referencia

- [Acerca del SDK de Voz](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [API REST: Speech-to-text](rest-speech-to-text.md) (API de REST: Voz a texto)
- [API REST: Text-to-speech](rest-text-to-speech.md) (API de REST: Texto a voz)
- [API REST: Batch transcription and customization](https://westus.cris.ai/swagger/ui/index) (API de REST: Transcripción y personalización de Azure Batch)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga una clave de suscripción gratuita a los servicios de Voz](get-started.md)
