---
title: Extracción de frases clave con Text Analytics REST API | Microsoft Docs
description: Extracción de frases clave con Text Analytics REST API desde Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 06/05/2019
ms.author: raymondl
ms.openlocfilehash: c803c85a0900a09b18909e2c81d52915a12cff1a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304074"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Ejemplo: Extracción de frases clave con Text Analytics

[Key Phrase Extraction API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) evalúa el texto no estructurado y, en cada documento JSON, devuelve una lista de frases clave. 

Esta capacidad es útil si necesita identificar rápidamente los principales puntos en una colección de documentos. Por ejemplo, si el texto de entrada es "La comida estaba deliciosa y el personal era maravilloso", el servicio devuelve los principales puntos de conversación: "comida" y "personal maravilloso".

Consulte el artículo de [idiomas admitidos](../text-analytics-supported-languages.md) para más información. 

> [!TIP]
> Text Analytics proporciona también una imagen de contenedor de Docker basada en Linux para la extracción de frases clave, por lo que puede [instalar y ejecutar el contenedor de Text Analytics](text-analytics-how-to-install-containers.md) cerca de los datos.

## <a name="preparation"></a>Preparación

La extracción de frases clave funciona mejor cuando se proporcionan cantidades más grandes de texto en los que trabajar. Es lo contrario del análisis de opiniones, que funciona mejor con cantidades de texto más pequeñas. Para obtener los mejores resultados de ambas operaciones, considere la posibilidad de reestructurar las entradas en consecuencia.

Debe tener documentos JSON en este formato: identificador, texto, idioma

El tamaño del documento debe ser inferior a 5120 caracteres por documento y puede tener hasta 1000 elementos (identificadores) por colección. La colección se envía en el cuerpo de la solicitud. El ejemplo siguiente es una ilustración del contenido que podría enviar a la extracción de frases clave.

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

Se pueden encontrar detalles sobre la definición de la solicitud en [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Cómo llamar a Text Analytics API). Recapitulamos los siguientes puntos para su comodidad:

+ Cree una solicitud **POST**. Revise la documentación de la API para esta solicitud: [Key Phrases API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)

+ Establezca el punto de conexión de HTTP para la extracción de frases clave, mediante un recurso de Text Analytics en Azure o un [contenedor de Text Analytics](text-analytics-how-to-install-containers.md) con instancias. Debe incluir el recurso `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`

+ Establezca un encabezado de solicitud para incluir la clave de acceso para las operaciones de Text Analytics. Para obtener más información, consulte [How to find endpoints and access keys](text-analytics-how-to-access-key.md) (Cómo buscar puntos de conexión y claves de acceso).

+ En el cuerpo de la solicitud, proporcione la colección de documentos JSON que preparó para este análisis.

> [!Tip]
> Use [Postman](text-analytics-how-to-call-api.md) o abra la **consola de prueba de la API** en la [documentación](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) para estructurar y enviar una solicitud POST al servicio.

## <a name="step-2-post-the-request"></a>Paso 2: Publicar la solicitud

El análisis se realiza tras la recepción de la solicitud. Consulte la sección de [límites de datos](../overview.md#data-limits) de la introducción para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo.

Recuerde que el servicio no tiene estado. No se almacena ningún dato en su cuenta. Los resultados se devuelven inmediatamente en la respuesta.

## <a name="step-3-view-results"></a>Paso 3: Visualización de los resultados

Todas las solicitudes POST devolverán una respuesta con formato JSON con los identificadores y las propiedades detectadas.

La salida se devuelve inmediatamente. Puede transmitir los resultados a una aplicación que acepte JSON o guardar la salida en un archivo en el sistema local y, a continuación, importarlo en una aplicación que permita ordenar, buscar y manipular los datos.

Aquí se muestra un ejemplo de salida de la extracción de frases clave:

```json
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
```

Como se indicó, el analizador busca y descarta las palabras que no son esenciales y conserva solo los términos o frases que parecen ser el asunto o el objeto de una frase. 

## <a name="summary"></a>Resumen

En este artículo, ha aprendido los conceptos y el flujo de trabajo de la extracción de frases clave mediante Text Analytics de Cognitive Services. En resumen:

+ [Key phrase extraction API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) está disponible para los idiomas seleccionados.
+ Los documentos JSON del cuerpo de la solicitud incluyen una id., texto y el código de idioma.
+ La solicitud POST se realiza a un punto de conexión `/keyphrases`, con una [clave de acceso y un punto de conexión](text-analytics-how-to-access-key.md) personalizados, que son válidos para la suscripción.
+ La salida de la respuesta, que consta de palabras clave y frases para cada id. de documento, se puede transmitir a cualquier aplicación que acepte JSON, incluidas Excel y Power BI, por nombrar algunas.

## <a name="see-also"></a>Otras referencias 

 [Información general de Text Analytics](../overview.md)  
 [Preguntas más frecuentes (P+F)](../text-analytics-resource-faq.md)</br>
 [Página del producto de Text Analytics](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Text Analytics API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
