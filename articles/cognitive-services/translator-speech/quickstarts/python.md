---
title: 'Inicio rápido: Translator Speech API, Python'
titlesuffix: Azure Cognitive Services
description: Obtenga información y ejemplos de código que le ayuden a empezar a usar rápidamente Translator Speech API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9426514853d58db1aa43eb42ee054b7bd931ae62
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356920"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Inicio rápido: Translator Speech API con Python
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

En este artículo se muestra cómo usar Translator Speech API para traducir palabras habladas en un archivo .wav.

## <a name="prerequisites"></a>Requisitos previos

Debe tener [Python.js 3.x](https://www.python.org/downloads/) para ejecutar este código.

Debe instalar el [paquete websocket-client](https://pypi.python.org/pypi/websocket-client) para Python.

Necesitará un archivo .wav llamado "speak.wav" en la misma carpeta que el ejecutable que compile con el código siguiente. Este archivo .wav debe estar en PCM estándar, formato mono de 16 bits, a 16 kHz.

Debe tener una [cuenta de Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con **Microsoft Translator Speech API**. Se requiere una clave de suscripción de pago del [panel de Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traducir voz

El código siguiente convierte la voz de un idioma a otro.

1. Cree un nuevo proyecto de Python en su IDE favorito.
2. Agregue el código que se proporciona a continuación.
3. Reemplace el valor `key` por una clave de acceso válida para la suscripción.
4. Ejecute el programa.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate'
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray()


def on_open(client):
    print("Connected.")

# r = read. b = binary.
    with open(input_file, mode='rb') as file:
        data = file.read()

    print("Sending audio.")
    client.send(data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print("Sending silence.")
    client.send(bytearray(32000), websocket.ABNF.OPCODE_BINARY)


def on_data(client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print("Received text data.")
        print(message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print("Received binary data.")
        print("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
            # w = write. b = binary.
            with open(output_file, mode='wb') as file:
                file.write(output)
                print("Wrote data to output file.")
            client.close()
    else:
        print("Received data of type: " + str(message_type))


def on_error(client, error):
    print("Connection error: " + str(error))


def on_close(client):
    print("Connection closed.")


client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print("Connecting...")
client.run_forever()
```

**Respuesta de Traducir voz**

Un resultado correcto es la creación de un archivo denominado "speak2.wav". El archivo contiene la traducción de las palabras habladas en "speak.wav".

[Volver arriba](#HOLTop)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial de Translator Speech](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Otras referencias

[Información general de Translator Speech](../overview.md)
[Referencia de API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
