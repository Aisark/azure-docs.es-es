---
title: Introducción a Web Language Model API
titleSuffix: Azure Cognitive Services
description: Web Language Model API en Microsoft Cognitive Services proporciona herramientas de última generación para el procesamiento de lenguaje natural.
services: cognitive-services
author: piyushbehre
manager: nitinme
ms.service: cognitive-services
ms.subservice: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: piyush
ROBOTS: NOINDEX
ms.openlocfilehash: 066c7f8dbd6f64ba40ec539b636922069ee8b925
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849905"
---
# <a name="what-is-the-web-language-model-api-preview"></a>¿Qué es Web Language Model API? (versión preliminar)

> [!IMPORTANT]
> El 9 de agosto de 2018 se retiró la versión preliminar de Web Language Model. Para el procesamiento y el análisis de texto, se recomienda usar los [módulos de análisis de texto de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

Web Language Model API es un servicio en la nube basado en REST que proporciona herramientas de última generación para el procesamiento de lenguaje natural. Con esta API, su aplicación puede aprovechar la eficacia de los macrodatos en los modelos de lenguaje entrenados en corpus de escala web recopilados por Bing en el mercado en-US.

Estos modelos de lenguaje de n-grama de retroceso suavizado, que admiten cadenas de Márkov de hasta quinto orden, se forman en función de los siguientes corpus:

- Texto de cuerpo de página web
- Texto de título de página web
- Texto de delimitador de página web
- Texto de consulta de búsqueda web

Web Language Model API es compatible con cuatro operaciones de búsqueda:

1. Probabilidad conjunta (log10) de una secuencia de palabras.
2. Probabilidad condicional (log10) de una palabra dada una secuencia de palabras anteriores.
3. Lista de palabras (finalizaciones) con mayor probabilidad de seguir una determinada secuencia de palabras.
4. Separación de palabras en cadenas que no contienen espacios.

## <a name="getting-started"></a>Introducción

1. Suscríbase al servicio.
2. Descargue el [SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Ejecute el código de ejemplo del SDK.
4. Consulte la [referencia de API](https://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51) para obtener detalles completos de los puntos de conexión, incluidos los fragmentos de código en una variedad de lenguajes.

## <a name="underlying-technology"></a>Tecnología subyacente

El siguiente documento proporciona información detallada sobre el desarrollo de estos modelos de lenguaje y debe citarse en las publicaciones de investigación que usen este servicio:

- [Introducción al corpus y las aplicaciones de n-grama web de Microsoft](https://research.microsoft.com/apps/pubs/default.aspx?id=130762) NAACL HLT 2010

Haga clic [aquí](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) para obtener una lista actual de los documentos que citan esta obra.
