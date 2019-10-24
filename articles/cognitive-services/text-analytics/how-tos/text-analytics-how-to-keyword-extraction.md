---
title: Extracción de frases clave con Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Cómo extraer frases clave con Text Analytics REST API desde Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429030"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Ejemplo: Extracción de frases clave con Text Analytics

[Key Phrase Extraction API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) evalúa el texto no estructurado y, en cada documento JSON, devuelve una lista de frases clave.

Esta capacidad es útil si necesita identificar rápidamente los principales puntos en una colección de documentos. Por ejemplo, si el texto de entrada es "La comida estaba deliciosa y el personal era maravilloso", el servicio devuelve los principales puntos de conversación: "comida" y "personal maravilloso".

Para más información, consulte [Lenguajes admitidos](../text-analytics-supported-languages.md).

> [!TIP]
> Text Analytics proporciona también una imagen de contenedor de Docker basada en Linux para la extracción de frases clave, por lo que puede [instalar y ejecutar el contenedor de Text Analytics](text-analytics-how-to-install-containers.md) cerca de los datos.

## <a name="preparation"></a>Preparación

La extracción de frases clave funciona mejor cuando se proporcionan cantidades más grandes de texto en los que trabajar. Es lo contrario del análisis de opiniones, que funciona mejor con cantidades de texto más pequeñas. Para obtener los mejores resultados de ambas operaciones, considere la posibilidad de reestructurar las entradas en consecuencia.

Debe tener documentos JSON en este formato: identificador, texto, idioma

El tamaño del documento debe ser de 5120 caracteres o menos por documento y puede tener hasta 1000 elementos (identificadores) por colección. La colección se envía en el cuerpo de la solicitud. El ejemplo siguiente es una ilustración del contenido que podría enviar a la extracción de frases clave.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Paso 1: Estructurar la solicitud

Para más información sobre la definición de la solicitud, consulte [Cómo llamar a la API REST de Text Analytics](text-analytics-how-to-call-api.md). Recapitulamos los siguientes puntos para su comodidad:

+ Cree una solicitud **POST**. Revise la documentación de la API para esta solicitud: [Key Phrases API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Establezca el punto de conexión de HTTP para la extracción de frases clave mediante un recurso de Text Analytics en Azure o un [contenedor de Text Analytics](text-analytics-how-to-install-containers.md) con instancias. Debe incluir `/text/analytics/v2.1/keyPhrases` en la dirección URL. Por ejemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Establezca un encabezado de solicitud para incluir la [clave de acceso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para las operaciones de Text Analytics.

+ En el cuerpo de la solicitud, proporcione la colección de documentos JSON que preparó para este análisis.

> [!Tip]
> Use [Postman](text-analytics-how-to-call-api.md) o abra la **consola de prueba de la API** en la [documentación](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) para estructurar y enviar una solicitud POST al servicio.

## <a name="step-2-post-the-request"></a>Paso 2: Publicar la solicitud

El análisis se realiza tras la recepción de la solicitud. Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto o por segundo, consulte la sección de [límites de datos](../overview.md#data-limits) de la introducción.

Recuerde que el servicio no tiene estado. No se almacena ningún dato en su cuenta. Los resultados se devuelven inmediatamente en la respuesta.

## <a name="step-3-view-results"></a>Paso 3: Visualización de los resultados

Todas las solicitudes POST devolverán una respuesta con formato JSON con los identificadores y las propiedades detectadas. El orden de las frases clave devueltas lo determina internamente el modelo.

La salida se devuelve inmediatamente. Puede transmitir los resultados a una aplicación que acepte JSON o guardar la salida en un archivo en el sistema local y, a continuación, importarlo en una aplicación que permita ordenar, buscar y manipular los datos.

Aquí se muestra un ejemplo de salida de la extracción de frases clave:

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

Como se indicó, el analizador busca y descarta las palabras que no son esenciales y conserva solo los términos o frases que parecen ser el asunto o el objeto de una frase.

## <a name="summary"></a>Resumen

En este artículo, ha aprendido los conceptos y el flujo de trabajo de la extracción de frases clave mediante Text Analytics de Cognitive Services. En resumen:

+ [Key phrase extraction API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) está disponible para los idiomas seleccionados.
+ Los documentos JSON del cuerpo de la solicitud incluyen un identificador, un texto y el código de idioma.
+ La solicitud POST se realiza a un punto de conexión `/keyphrases`, con una [clave de acceso y un punto de conexión](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizados, que son válidos para la suscripción.
+ La salida de la respuesta, que consta de palabras clave y frases para cada id. de documento, se puede transmitir a cualquier aplicación que acepte JSON, incluidas Microsoft Office Excel y Power BI, por nombrar algunas.

## <a name="see-also"></a>Otras referencias

 [Introducción a Text Analytics](../overview.md) [Preguntas más frecuentes](../text-analytics-resource-faq.md)</br>
 [Página del producto de Text Analytics](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Text Analytics API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
