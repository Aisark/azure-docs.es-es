---
title: Consulta del punto de conexión del contenedor Conversión de texto a voz
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/28/2019
ms.locfileid: "73491195"
---
El contenedor proporciona [API de punto de conexión basadas en REST](../rest-text-to-speech.md). Hay muchos [proyectos de código fuente de ejemplo](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) disponibles para las variaciones de lenguaje, marco y plataforma.

Con el contenedor *Conversión de texto a voz estándar*, debe basarse en la configuración regional y en la voz de la etiqueta de imagen que descargó. Por ejemplo, si descargó la etiqueta `latest`, la configuración regional predeterminada es `en-US` y la voz `JessaRUS`. El argumento `{VOICE_NAME}` sería [`en-US-JessaRUS`](../language-support.md#standard-voices). Vea el SSML de ejemplo siguiente:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Sin embargo, para *Conversión de texto a voz personalizada*, tendrá que obtener el valor de **Voice / model** desde el [portal de Voz personalizada](https://aka.ms/custom-voice-portal). El nombre del modelo personalizado es sinónimo del nombre de la voz. Vaya a la página de **entrenamiento** y copie el valor de **Voice / model** que se va a usar como el argumento `{VOICE_NAME}`.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modelo de Voz personalizada: nombre de voz":::

Vea el SSML de ejemplo siguiente:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Vamos a crear una solicitud HTTP POST, proporcionando algunos encabezados y una carga de datos. Reemplace el marcador de posición `{VOICE_NAME}` por un valor propio.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Este comando:

* Construye una solicitud HTTP POST para el punto de conexión `speech/synthesize/cognitiveservices/v1`.
* Especifica un encabezado `Accept` de `audio/*`
* Especifica un encabezado `Content-Type` de `application/ssml+xml`. Para más información, consulte el [cuerpo de la solicitud](../rest-text-to-speech.md#request-body).
* Especifica un encabezado `X-Microsoft-OutputFormat` de `riff-16khz-16bit-mono-pcm`. Para más opciones, consulte la [salida de audio](../rest-text-to-speech.md#audio-outputs).
* Envía la solicitud [Lenguaje de marcado de síntesis de voz (SSML)](../speech-synthesis-markup.md) dado el `{VOICE_NAME}` al punto de conexión.