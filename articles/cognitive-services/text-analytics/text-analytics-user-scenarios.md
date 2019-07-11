---
title: Ejemplos de escenarios de usuario de Text Analytics API
titleSuffix: Azure Cognitive Services
description: Use este artículo para ver algunos escenarios comunes para integrar Text Analytics API en sus servicios y procesos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: d8d2aed16b5af60c44501e6d72332783a164ff91
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478385"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Ejemplos de escenarios de usuario de Text Analytics API

Text Analytics API es un servicio basado en la nube que realiza el procesamiento del lenguaje natural avanzado en texto. En este artículo se describen algunos ejemplos de casos de uso de la integración de la API en sus soluciones y procesos empresariales. 

## <a name="analyze-survey-results"></a>Analizar resultados de encuestas

Procese las respuestas de texto sin formato mediante el Análisis de sentimiento y extraiga conclusiones a partir de los resultados de las encuestas a los empleados y clientes. Agregue los resultados para realizar análisis y un seguimiento, así como para aumentar la involucración de las personas.

![Imagen que describe cómo realizar análisis de sentimiento sobre las encuestas a clientes y empleados.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analizar las llamadas entrantes grabadas de los clientes

Extraiga estadísticas de las llamadas de los servicios al cliente mediante texto a voz, Análisis de sentimiento y Extracción de frases clave. Muestre los resultados en el panel de Power BI o un portal para comprender mejor a los clientes, resalte las tendencias del servicio al cliente y aumente la involucración del cliente. Envíe solicitudes a la API en forma de lote para generar informes o en tiempo real para la intervención. Consulte el [código de ejemplo](https://github.com/rlagh2/callcenteranalytics).

![Imagen que describe cómo automatizar la obtención de estadísticas de llamadas del servició al cliente mediante el análisis de sentimiento](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Procesar y clasificar los incidentes de soporte técnico

Use la Extracción de frases clave y el reconocimiento de entidades para procesar solicitudes de soporte técnico enviadas en formato de texto no estructurado. Use las entidades y las frases extraídas para clasificar las solicitudes para el análisis de tendencias y el planeamiento de recursos.

![Imagen que se describe cómo usar el reconocimiento de entidades y la Extracción de frases clave para clasificar los informes de incidentes y las tendencias](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Supervisar las fuentes de sus productos en las redes sociales

Supervise los comentarios de los usuarios sobre productos en la página de Twitter o Facebook de su producto. Use los datos para analizar el sentimiento del cliente acerca del lanzamiento de nuevos productos, extraiga frases clave sobre características y solicitudes de características, o bien aborde las quejas de los clientes a medida que surgen.

![Imagen que describe cómo supervisar los comentarios sobre su empresa y producto en las redes sociales mediante la Extracción de frases clave](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es Text Analytics API?](overview.md)
* [Envío de una solicitud a Text Analytics API mediante C#](quickstarts/csharp.md)
