---
title: 'Qué es la transcripción de conversaciones (versión preliminar): servicio Voz'
titleSuffix: Azure Cognitive Services
description: La transcripción de conversaciones es una solución de conversión de voz a texto que combina el reconocimiento de voz, la identificación del hablante y la atribución de oraciones a cada hablante (lo que también se conoce como diarización) para proporcionar la transcripción asincrónica o en tiempo real de cualquier conversación.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 0c15b053028bd707159a632c98faaea8b9954a9b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075843"
---
# <a name="what-is-conversation-transcription-preview"></a>¿Qué es la transcripción de conversaciones (versión preliminar)?

La transcripción de conversaciones es una solución de [conversión de voz a texto](speech-to-text.md) que combina el reconocimiento de voz, la identificación del hablante y la atribución de oraciones a cada hablante (lo que también se conoce como _diarización_) para proporcionar la transcripción asincrónica o en tiempo real de cualquier conversación. La transcripción de conversaciones distingue a los hablantes de una conversación para determinar quién dijo qué y cuándo, y facilita a los desarrolladores la tarea de agregar conversión de voz a texto a sus aplicaciones que realizan la diarización de varios hablantes.

## <a name="key-features"></a>Principales características

- **Marcas de tiempo**: cada expresión del hablante tiene una marca de tiempo, por lo que puede encontrar fácilmente cuándo se dijo una frase.
- **Transcripciones legibles**: el formato y la puntuación de las transcripciones se agregan automáticamente para garantizar que el texto coincide lo más posible con lo que se ha dicho.
- **Perfiles de usuario**: los perfiles de usuario se generan mediante la recopilación de muestras de voz de usuarios y el envío de estas a la generación de firmas.
- **Identificación del hablante**: los hablantes se identifican mediante perfiles de usuario; a cada hablante se le asigna un _identificador de hablante_.
- **Diarización de varios hablantes**: determine quién dijo qué mediante la sintetización de la secuencia de audio con cada identificador de hablante.
- **Transcripción en tiempo real**: proporcione transcripciones en directo de quién dice qué y en qué momento mientras tiene lugar la conversación.
- **Transcripción asincrónica**: proporcione transcripciones con una mayor precisión mediante una secuencia de audio de varios canales.

> [!NOTE]
> Aunque la transcripción de conversaciones no impone un límite sobre el número de hablantes en la sala, está optimizada para entre 2 y 10 hablantes por sesión.

## <a name="use-cases"></a>Casos de uso

### <a name="inclusive-meetings"></a>Reuniones inclusivas

Para que las reuniones sean inclusivas para todo el mundo, por ejemplo, para los participantes sordos y con dificultades auditivas, es importante contar con transcripción en tiempo real. La transcripción de conversaciones en tiempo real toma el audio de una reunión y determina quién dice qué, lo que permite a todos los participantes seguir la transcripción y participar en la reunión sin ningún retraso.

### <a name="improved-efficiency"></a>Mayor eficacia

Los participantes en la reunión pueden centrarse en la reunión y dejar la toma de notas a la transcripción de conversaciones. De este modo, pueden participar activamente en la reunión y realizar un seguimiento rápido de los pasos que vienen a continuación valiéndose de la transcripción en lugar de tomar notas y perderse algo posiblemente durante el evento.

## <a name="how-it-works"></a>Cómo funciona

Así es a grandes rasgos cómo funciona la transcripción de conversaciones.

![Diagrama de importación de Transcripción de conversaciones](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Entradas esperadas

- **Secuencia de audio de varios canales**: para especificaciones y detalles de diseño, consulte [Micrófono del SDK de dispositivos de voz de Microsoft](https://aka.ms/cts/microphone). Para más información o comprar un kit de desarrollo, consulte cómo [obtener Microsoft Speech Devices SDK](https://aka.ms/cts/getsdk).
- **Muestras de voz de usuarios**: la transcripción de conversaciones necesita perfiles de usuario antes de la conversación. Deberá recopilar grabaciones de audio de cada usuario y, luego, enviarlas al [servicio de generación de firmas](https://aka.ms/cts/signaturegenservice) para validar el audio y generar los perfiles de usuario.

## <a name="real-time-vs-asynchronous"></a>Tiempo real frente a asincrónico

La transcripción de conversaciones ofrece tres modos de transcripción:

### <a name="real-time"></a>Tiempo real

Los datos de audio se procesan en directo para devolver el identificador y la transcripción del hablante. Seleccione este modo si quiere usar la solución de transcripción para proporcionar a los participantes de la conversación una vista de la transcripción en directo de su conversación en curso. Por ejemplo, crear una aplicación para que las reuniones sean más accesibles para los participantes sordos y con dificultades auditivas es un caso de uso idóneo para la transcripción en tiempo real.

### <a name="asynchronous"></a>Asincrónico

Los datos de audio se procesan por lotes para devolver el identificador y la transcripción del hablante. Seleccione este modo si quiere usar la solución de transcripción para proporcionar una mayor precisión sin la vista de la transcripción en directo. Por ejemplo, si quiere crear una aplicación para permitir que los participantes de la reunión se pongan al día fácilmente con las reuniones a las que han faltado, use el modo de transcripción asincrónica para obtener resultados de transcripción de alta precisión.

### <a name="real-time-plus-asynchronous"></a>Tiempo real y asincrónico

Los datos de audio se procesan en directo para devolver el identificador y la transcripción del hablante; además, se crea una solicitud para obtener también una transcripción de alta precisión mediante el procesamiento asincrónico. Seleccione este modo si la aplicación necesita una transcripción en tiempo real, pero también requiere una transcripción de mayor precisión después de la conversación o reunión.

## <a name="language-support"></a>Compatibilidad con idiomas

Actualmente, la transcripción de conversaciones está disponible para los idiomas "en-US" y "zh-CN" en las siguientes regiones: *centralus* y  *eastasia*. Si necesita ayuda adicional con la configuración regional, póngase en contacto con el [equipo de la característica Transcripción de conversaciones](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Transcripción de conversaciones en tiempo real](how-to-use-conversation-transcription-service.md)
