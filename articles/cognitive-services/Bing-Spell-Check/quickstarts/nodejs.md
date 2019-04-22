---
title: 'Inicio rápido: Revisión ortográfica con la API REST de Bing Spell Check y Node.js'
titlesuffix: Azure Cognitive Services
description: Introducción al uso de la API REST de Bing Spell Check para la revisión ortográfica y gramatical.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: 0a1260de6428f6ebc70757261cdcc3002820ec7b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547771"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Inicio rápido: Revisión ortográfica con la API REST de Bing Spell Check y Node.js

Use este inicio rápido para realizar la primera llamada a la API REST de Bing Spell Check. Esta sencilla aplicación de Node envía una solicitud a la API y devuelve una lista de palabras que no ha reconocido, seguida de las correcciones sugeridas. Si bien esta aplicación está escrita en Node.js, la API es un servicio web RESTful compatible con la mayoría de los lenguajes de programación. El código fuente de esta aplicación está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Requisitos previos

* [Node.js 6](https://nodejs.org/en/download/) o posterior.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Creación e inicialización de un proyecto

1. Cree un archivo de JavaScript en su IDE o editor favorito. Establezca la rigurosidad y que se requiera `https`. A continuación, cree variables para el host, la ruta de acceso y la clave de suscripción del punto de conexión de API.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Create las variables de los parámetros de búsqueda y el texto que desea comprobar. Anexe el código de mercado después de `mkt=`. El código de mercado es el país desde el que se realiza la solicitud. Anexe también el modo de revisión ortográfica después de `&mode=`. El modo es `proof` (detecta la mayoría de los errores ortográficos y gramaticales) o `spell` (detecta la mayoría de los errores ortográficos, pero no todos los gramaticales).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Creación de los parámetros de solicitud

Para crear los parámetros de solicitud, cree un objeto con un método `POST`. Agregue la ruta de acceso mediante la anexión de la ruta de acceso del punto de conexión y la cadena de consulta. Agregue la clave de suscripción al encabezado `Ocp-Apim-Subscription-Key`.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Creación de un controlador de respuesta

Cree una función denominada `response_handler` que tome la respuesta JSON de la API e imprímala. Cree una variable para el cuerpo de la respuesta. Anexe la respuesta cuando se reciba la marca `data`, con `response.on()`. Cuando se reciba una marca `end`, imprima el cuerpo JSON en la consola.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Envío de la solicitud

Llame a la API mediante `https.request()` con los parámetros de solicitud y el controlador de respuesta. Escriba el texto en la API y finalice la solicitud.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>Ejemplo de respuesta JSON

Se devuelve una respuesta correcta en JSON, como se muestra en el siguiente ejemplo:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de una aplicación web de una sola página](../tutorials/spellcheck.md)

- [¿Qué es Bing Spell Check API?](../overview.md)
- [Referencia de Bing Spell Check API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
