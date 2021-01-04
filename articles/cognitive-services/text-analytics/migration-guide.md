---
title: Guía de migración para la versión 2 de la API Text Analytics
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo migrar sus aplicaciones para que usen la versión 3 de la API Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 6a71bcbfb8341098711e330cebf8545e1fd2751c
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656961"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migración a la versión 3.x de la API Text Analytics

Si usa la versión 2.1 de la API Text Analytics, este artículo le ayudará a actualizar la aplicación para que use la versión 3.x. La versión 3.0 está disponible con carácter general y presenta nuevas características, como [Reconocimiento de entidades con nombre (NER)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) expandido y el [control de versiones del modelo](concepts/model-versioning.md). También hay disponible una versión preliminar de v3.1 (v 3.1-Preview.x), que agrega características como la [minería de datos](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). Los modelos que se usan en v2 no recibirán actualizaciones futuras. 

## <a name="sentiment-analysis"></a>[Análisis de opiniones](#tab/sentiment-analysis)

### <a name="feature-changes"></a>Cambios de características 

Análisis de sentimiento en la versión 2.1 devuelve las puntuaciones de opinión entre 0 y 1 para cada documento enviado a la API, con puntuaciones más próximas a 1 que indican una opinión más positiva. En su lugar, la versión 3 devuelve etiquetas de opinión (como "positivo" o "negativo") para las oraciones y el documento en su totalidad, así como sus puntuaciones de confianza asociadas. 

### <a name="steps-to-migrate"></a>Pasos para la migración

#### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud al punto de conexión v3 para el análisis de opiniones. Por ejemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`. También necesitará actualizar la aplicación para usar las etiquetas de opinión devueltas en la [respuesta de la API](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Versión 3.1: versión preliminar](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>Bibliotecas de clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER y vinculación de entidad](#tab/named-entity-recognition)

### <a name="feature-changes"></a>Cambios de características

> [!NOTE] 
> Actualmente, las [categorías de entidades v3](named-entity-types.md) solo se devuelven en texto en inglés y español. La API devuelve los resultados de la versión 2.1 para las solicitudes en otros idiomas, siempre que se admitan en la versión 2.1.

En la versión 2.1, la API Text Analytics utiliza un punto de conexión para Reconocimiento de entidades con nombre (NER) y la vinculación de entidad. La versión 3 proporciona detección de entidades con nombre expandida y usa puntos de conexión independientes para las solicitudes de vinculación de entidad y NER. A partir de la versión de 3.1-Preview. 1, NER puede detectar además la información personal `pii` y de salud `phi`. 

### <a name="steps-to-migrate"></a>Pasos para la migración

#### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud a los puntos de conexión v3 para NER o la vinculación de entidad.

Entity Linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

También necesitará actualizar la aplicación para usar las [categorías de entidad](named-entity-types.md) devueltas en la [respuesta de la API](how-tos/text-analytics-how-to-entity-linking.md#view-results).

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Versión 3.1: versión preliminar](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Bibliotecas de clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="language-detection"></a>[Detección de idioma](#tab/language-detection)

### <a name="feature-changes"></a>Cambios de características 

La característica de detección de idioma no ha cambiado en la versión 3 fuera de la versión del punto de conexión, pero la respuesta JSON contendrá `ConfidenceScore` en lugar de `score`. La versión 3 también devuelve solo un idioma en la salida. 

### <a name="steps-to-migrate"></a>Pasos para la migración

#### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud al punto de conexión v3 para la detección de idioma. Por ejemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`. También necesitará actualizar la aplicación para usar `ConfidenceScore` en lugar de `score` en la [respuesta de la API](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Versión 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>Bibliotecas de clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Extracción de frases clave](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>Cambios de características 

La característica de extracción de frases clave no ha cambiado en la versión 3 fuera de la versión del punto de conexión.

### <a name="steps-to-migrate"></a>Pasos para la migración

#### <a name="rest-api"></a>API DE REST

Si la aplicación usa la API REST, actualice su punto de conexión de solicitud al punto de conexión v3 para la extracción de frases clave. Por ejemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Consulte la documentación de referencia para obtener ejemplos de la respuesta JSON.
* [Versión 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Versión 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Versión 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>Bibliotecas de clientes

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Consulte también

* [¿Qué es la API Text Analytics?](overview.md)
* [Compatibilidad con idiomas](language-support.md)
* [Control de versiones de los modelos](concepts/model-versioning.md)
