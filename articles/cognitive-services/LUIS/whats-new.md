---
title: Novedades de Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Este artículo se actualiza periódicamente con noticias sobre la API de Language Understanding de Azure Cognitive Services.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 62ce0ac36223098d013cc75b7f62fc2c68e358ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953382"
---
# <a name="whats-new-in-language-understanding"></a>Novedades de Language Understanding

Conozca las novedades del servicio. Estos elementos incluyen notas de la versión, vídeos, entradas de blogs y otros tipos de información. Marque esta página para mantenerse actualizado con el servicio.  

## <a name="release-notes"></a>Notas de la versión 

### <a name="november-4-2019---ignite"></a>4 de noviembre de 2019: Ignite

* Mayor productividad de los desarrolladores
    * Disponibilidad general de nuestro [punto de conexión de predicción V3](luis-migration-api-v3.md). 
    * Capacidad de importar y exportar aplicaciones con el formato .lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Esto prepara el camino para un proceso de CI/CD efectivo. 
* Expansión de idioma
    * [Árabe e hindi](luis-language-support.md) en versión preliminar pública.
* Modelos creados previamente
    * Los [dominios precompilados](luis-reference-prebuilt-domains.md) ya están disponibles con carácter general (GA)
    * [Entidades precompiladas](luis-reference-prebuilt-entities.md#japanese-entity-support) en japonés: edad, moneda, número y porcentaje no se admiten en la versión V3.
    * [Entidades precompiladas](luis-reference-prebuilt-entities.md#italian-entity-support) en italiano: edad, moneda, dimensión, número y resolución porcentual se cambiaron desde la versión V2.
* Mejore la experiencia del usuario en el [portal de preview.luis.ai](https://preview.luis.ai): una experiencia de etiquetado renovada para habilitar la compilación y depuración de modelos complejos. Pruebe los tutoriales del portal de versión preliminar:
    * [Solo intenciones](tutorial-intents-only.md)
    * [Entidad de aprendizaje automático que se puede descomponer](tutorial-machine-learned-entity.md) 
* Funcionalidades de comprensión del lenguaje avanzadas: [cree modelos de lenguaje sofisticados](luis-concept-entity-types.md) con menos esfuerzo. 
* Defina características de aprendizaje automático en el nivel de modelo y permita que los modelos se usen como señales para otro modelo; por ejemplo, use entidades como características para las intenciones y otras entidades.
* [Límites](luis-boundaries.md) nuevos y ampliados: un límite máximo más elevado para las listas de frases y las frases totales, nuevos límites de modelo como característica
* Extraiga información de texto en el formato de la estructura de jerarquía profunda, lo que permite que las aplicaciones de conversación sean más eficaces.

    ![Imagen de entidad de aprendizaje automático](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 de septiembre de 2019

* Recurso de creación de Azure: [migrar ahora](luis-migration-authoring.md).
    * 500 aplicaciones por recurso de Azure
    * 100 versiones por aplicación
* Compatibilidad del turco con las entidades precompiladas
* Compatibilidad del italiano con datetimeV2

### <a name="july-23-2019"></a>23 de julio de 2019

* Actualización de [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) a 1.2.3
    * Reconocedores de edad, temperatura, dimensión y moneda en italiano.
    * Mejora en el reconocimiento de festividades en inglés para calcular correctamente las fechas basadas en el Día de Acción de Gracias.
    * Mejoras en DateTime en francés para reducir los falsos positivos de entidades que no son de fecha y hora.
    * Compatibilidad con el año natural, escolar o fiscal y acrónimos en DateRange en inglés.
    * Reconocimiento de PhoneNumber mejorado en chino y japonés.
    * Compatibilidad mejorada con NumberRange en inglés.
    * Mejoras en el rendimiento.

### <a name="june-24-2019"></a>24 de junio de 2019

* [Entidad creada previamente OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) para permitir la ordenación, como siguiente, anterior y último. Referencia cultural inglesa únicamente.

### <a name="may-6-2019---build-conference"></a>6 de mayo de 2019: conferencia Build

Las siguientes características se presentaron en el congreso Build 2019:

* [Versión preliminar de la guía de migración de la API v3](luis-migration-api-v3.md)
* [Panel de análisis mejorado](luis-how-to-use-dashboard.md)
* [Dominios creados previamente mejorados](luis-reference-prebuilt-domains.md) 
* [Entidades de lista dinámica](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Vídeos

### <a name="2019-build-videos"></a>Vídeos de Build 2019

[How to use Azure Conversational AI to scale your business for the next generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be) (Cómo usar la inteligencia artificial de Azure Conversational para llevar su negocio a la próxima generación)

## <a name="service-updates"></a>Actualizaciones del servicio

[Anuncios de actualización de Azure para Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
