---
title: Glosario
titleSuffix: Language Understanding - Azure Cognitive Services
description: En el glosario se explican los términos que puede encontrar cuando use el servicio de API de LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 3fc14b821064719f39eb877aa2253f8084496a66
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523494"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glosario de Language Understanding de conceptos y vocabulario común
En el glosario de Language Understanding (LUIS) se explican los términos que pueden aparecer cuando se trabaja con el servicio de API de LUIS.

## <a name="active-version"></a>Versión activa

La versión activa de LUIS es la versión que recibe los cambios que se hagan en el modelo. En el sitio web de [LUIS](luis-reference-regions.md), si quiere realizar cambios en una versión que no sea la versión activa, primero debe establecer esa versión como activa.

## <a name="authoring"></a>Creación

La creación es la capacidad de crear, administrar e implementar una [aplicación de LUIS](#luis-app), ya sea mediante el sitio web de [LUIS](luis-reference-regions.md) o las [API de creación](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Clave de creación

Antes se denominaba clave "de programación". Se usa para crear la aplicación. No se usa para enviar consultas de punto de conexión de nivel de producción. Para obtener más información, vea [Key limits](luis-boundaries.md#key-limits) (Límites de la clave).   

## <a name="batch-test-json-file"></a>Archivo JSON de texto por lotes

El archivo por lotes es una matriz JSON. Cada elemento de la matriz tiene tres propiedades: `text`, `intent` y `entities`. La propiedad `entities` es una matriz. La matriz puede estar vacía. Si la matriz `entities` no está vacía, necesita identificar las entidades con precisión.

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>Colaborador

Un colaborador no es el [propietario](#owner) de la aplicación, pero tiene los mismos permisos para agregar, editar y eliminar las intenciones, entidades y expresiones.

## <a name="currently-editing"></a>En edición

Es lo mismo que la [versión activa](#active-version).

## <a name="domain"></a>Dominio

En el contexto de LUIS, un **dominio** es un área de conocimiento. El dominio es específico del área de conocimiento de la aplicación. Puede tratarse de un área general, como la aplicación de la agencia de viajes. La aplicación de una agencia de viajes también puede ser específica solo para las áreas de información de su empresa, por ejemplo, ubicaciones geográficas, idiomas y servicios en concreto.

## <a name="endpoint"></a>Punto de conexión

La dirección URL del [punto de conexión de LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) es donde se envían las consultas de LUIS después de crear y publicar la [aplicación de LUIS](#luis-app). La dirección URL del punto de conexión contiene la región de la aplicación publicada, así como el identificador de la aplicación. Puede encontrar el punto de conexión en la página **[Claves y puntos de conexión](luis-how-to-azure-subscription.md)** de la aplicación o bien puede obtener la dirección URL del punto de conexión desde la API [Get App Info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37).

Un punto de conexión de ejemplo tiene el siguiente aspecto:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Parámetro Querystring|description|
|--|--|
|region| [región publicada](luis-reference-regions.md#publishing-regions) |
|appID | Identificador de la aplicación de LUIS |
|subscriptionID | Clave de punto de conexión (suscripción) de LUIS creada en Azure Portal |
|q | expresión |
|timezoneOffset| minutes|

## <a name="entity"></a>Entidad

Las [entidades](luis-concept-entity-types.md) son palabras importantes en las [expresiones](luis-concept-utterance.md) que describen información relevante para la [intención](luis-concept-intent.md) y a veces son esenciales. Una entidad es básicamente un tipo de datos de LUIS.

## <a name="f-measure"></a>Medida F

En las [pruebas por lotes](luis-interactive-test.md#batch-testing), una medida de la precisión de la prueba.

## <a name="false-negative"></a>Falso negativo (TN)

En las [pruebas por lotes](luis-interactive-test.md#batch-testing), los puntos de datos representan expresiones en las que la aplicación predice incorrectamente la ausencia de la intención o entidad de destino.

## <a name="false-positive"></a>Falso positivo (TP)

En las [pruebas por lotes](luis-interactive-test.md#batch-testing), los puntos de datos representan expresiones en las que la aplicación predice incorrectamente la existencia de la intención o entidad de destino.

## <a name="features"></a>Características

En el aprendizaje automático, una [característica](luis-concept-feature.md) es un rasgo distintivo o un atributo de datos que el sistema observa.

## <a name="intent"></a>Intención

Una [intención](luis-concept-intent.md) representa una tarea o acción que el usuario quiere realizar. Es un propósito u objetivo que está expresado en la entrada de un usuario, como reservar un vuelo, pagar una factura o buscar un artículo de prensa. En LUIS, la predicción de la intención se basa en la expresión completa. Las entidades, en comparación, son partes de una expresión.

## <a name="labeling"></a>Etiquetado

El etiquetado es el proceso de asociar una palabra o frase en la [expresión](#utterance) de una intención con una [entidad](#entity) (tipo de datos).

## <a name="luis-app"></a>Aplicación de LUIS

Una aplicación de LUIS es un modelo de datos entrenado para el procesamiento del lenguaje natural e incluye [intenciones](#intent), [entidades](#entity) y [expresiones](#utterance) etiquetadas.

## <a name="owner"></a>Propietario

Cada aplicación tiene un propietario, que es la persona que la ha creado. El propietario puede agregar [colaboradores](#collaborator).

## <a name="pattern"></a>Patrones
La característica Patrón anterior se reemplaza con [Patrones](luis-concept-patterns.md). Use patrones para mejorar la precisión de la predicción al proporcionar menos ejemplos de aprendizaje.

## <a name="phrase-list"></a>Lista de frases

Una [lista de frases](luis-concept-feature.md#what-is-a-phrase-list-feature) incluye un grupo de valores (palabras o frases) que pertenecen a la misma clase y que se deben tratar de forma similar (por ejemplo, nombres de ciudades o productos). Una lista intercambiable se trata como si fueran sinónimos.

## <a name="prebuilt-domains"></a>Dominio creado previamente

Un [dominio creado previamente](luis-how-to-use-prebuilt-domains.md) es una aplicación de LUIS configurada para un dominio específico, como la automatización del hogar (HomeAutomation) o las reservas de un restaurante (RestaurantReservation). Las intenciones, expresiones y entidades están configuradas para este dominio.

## <a name="prebuilt-entity"></a>Entidad creada previamente

Una [entidad creada previamente](luis-prebuilt-entities.md) es una entidad que LUIS proporciona para los tipos comunes de información, por ejemplo, número, dirección URL y correo electrónico. Puede agregar una entidad creada previamente a la aplicación.

## <a name="precision"></a>Precisión
En las [pruebas por lotes](luis-interactive-test.md#batch-testing), la precisión (también conocida como el valor de predicción positivo) es la fracción de expresiones relevantes entre las expresiones que se recuperan.

## <a name="programmatic-key"></a>Clave de programación

Ha cambiado de nombre, ahora es la [clave de creación](#authoring-key).

## <a name="publish"></a>Publicar

Publicar significa poner una [versión activa](#active-version) de LUIS a disposición del [punto de conexión](#endpoint) del almacenamiento provisional o de producción.  

## <a name="quota"></a>Cuota

La cuota de LUIS es la limitación del [nivel de suscripción de Azure](https://aka.ms/luis-price-tier). La cuota de LUIS puede verse limitada por las solicitudes por segundo (estado HTTP 429) y el total de solicitudes en un mes (estado HTTP 403).

## <a name="recall"></a>Recuperación
En las [pruebas por lotes](luis-interactive-test.md#batch-testing), la recuperación (también conocida como susceptibilidad), es la capacidad de generalizar de LUIS.

## <a name="semantic-dictionary"></a>Diccionario de semántica
Se proporciona un diccionario de semántica en la página de entidad de lista, así como en la página de lista de frases. El diccionario de semántica proporciona sugerencias de palabras en función del ámbito actual.

## <a name="sentiment-analysis"></a>Análisis de sentimiento
El análisis de sentimiento proporciona valores positivos o negativos de las expresiones que proporciona [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Preparación para la voz

La preparación para la voz permite que el servicio de voz se prepare con el modelo de LUIS.

## <a name="spelling-correction"></a>Corrección ortográfica

Habilite el corrector ortográfico de Bing para corregir las palabras incorrectas de las expresiones antes de la predicción.

## <a name="starter-key"></a>Clave de inicio

Al igual que la [clave de programación](#programmatic-key), su nombre ha cambiado a clave de creación.

## <a name="subscription-key"></a>Clave de suscripción

La clave de suscripción es la clave de **punto de conexión** asociada con el servicio de LUIS que [ha creado en Azure](luis-how-to-azure-subscription.md). Esta no es la [clave de creación](#programmatic-key). Si tiene una clave de punto de conexión, se debe usar para las solicitudes de punto de conexión en lugar de la clave de creación. Puede ver la clave de punto de conexión actual en la dirección URL del punto de conexión, en la parte inferior de la página [**Claves y puntos de conexión**](luis-how-to-azure-subscription.md) del sitio web de [LUIS](luis-reference-regions.md). Es el valor del par nombre/valor **subscription-key**.

## <a name="test"></a>Probar

[Probar](luis-interactive-test.md#test-your-app) una aplicación de LUIS significa pasar una expresión a LUIS y ver los resultados de JSON.

## <a name="timezoneoffset"></a>Desplazamiento de zona horaria

El punto de conexión incluye timezoneOffset. Este es el número de minutos que quiere agregar a la entidad datetimeV2 creada previamente, o bien quitar de esta. Por ejemplo, si la expresión es "¿Qué hora es?", el valor de datetimeV2 que se devuelve es la hora actual de la solicitud del cliente. Si la solicitud del cliente viene de un bot u otra aplicación que no es el mismo que el usuario de su bot, debe pasar el desplazamiento entre el bot y el usuario.

Vea [Cambiar la zona horaria de la entidad datetimeV2 creada previamente](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Un token es la unidad más pequeña que se puede etiquetar en una entidad. La tokenización se basa en la [referencia cultural](luis-language-support.md#tokenization) de la aplicación.

## <a name="train"></a>Entrenar

Entrenar es el proceso de enseñar a LUIS los cambios de la [versión activa](#active-version) desde el último aprendizaje.

## <a name="true-negative"></a>Verdadero negativo (TN)

En las [pruebas por lotes](luis-interactive-test.md#batch-testing), los puntos de datos representan expresiones en las que la aplicación predice correctamente la ausencia de la intención o entidad de destino.

## <a name="true-positive"></a>Verdadero positivo (TP)

En las [pruebas por lotes](luis-interactive-test.md#batch-testing), los puntos de datos representan expresiones en las que la aplicación predice correctamente la existencia de la intención o entidad de destino.

## <a name="utterance"></a>Expresión

Una expresión es una frase en lenguaje natural, como "reserva 2 billetes a Seattle para el próximo martes". Se agregan expresiones de ejemplo a la intención.

## <a name="version"></a>Versión

Una [versión](luis-how-to-manage-versions.md) de LUIS es un modelo de datos específico asociado con un identificador de aplicación de LUIS y el punto de conexión publicado. Todas las aplicaciones de LUIS tienen al menos una versión.
