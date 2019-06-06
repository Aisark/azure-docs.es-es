---
title: ¿Qué es Bing Autosuggest?
titlesuffix: Azure Cognitive Services
description: Aprenda a usar Bing Autosuggest API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 24f35d795b34e7d9c214a23c040791841b4a711b
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382568"
---
# <a name="what-is-bing-autosuggest"></a>¿Qué es Bing Autosuggest?

Si una aplicación envía consultas a cualquier Bing Search API, puede usar Bing Autosuggest API para mejorar su experiencia de búsqueda. Bing Autosuggest API devuelve una lista de consultas sugeridas según la cadena de consulta parcial del cuadro de búsqueda. Como los caracteres se escriben en el cuadro de búsqueda, puede mostrar sugerencias en una lista desplegable.

## <a name="bing-autosuggest-api-features"></a>Características de Bing Autosuggest API

| Característica                                                                                                                                                                                 | DESCRIPCIÓN                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerencia de términos de búsqueda en tiempo real](concepts/get-suggestions.md) | Mejore su experiencia con las aplicaciones mediante Autosuggest API para mostrar términos de búsqueda sugeridos a medida que se escriben. |

## <a name="workflow"></a>Flujo de trabajo

Bing Autosuggest API es un servicio web RESTful, fácil de llamar desde cualquier lenguaje de programación que pueda realizar solicitudes HTTP y analizar JSON. 

1. Cree una [cuenta de API de Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con acceso a Bing Search APIs. Si no tiene una suscripción de Azure, puede [crear una cuenta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envíe una solicitud a esta API cada vez que un usuario escriba un carácter en el cuadro de búsqueda de la aplicación.
3. Analice el mensaje JSON devuelto para procesar la respuesta de API.

Generalmente, llamaría a esta API cada vez que un usuario escriba un carácter en el cuadro de búsqueda de la aplicación. A medida que se escriben más caracteres, la API devolverá las consultas de búsqueda sugeridas más relevantes. Por ejemplo, las sugerencias que la API podría devolver para un único `s` suelen ser menos pertinentes que para `sail`.

El ejemplo siguiente muestra un cuadro de búsqueda desplegable con los términos de consulta sugeridos de Bing Autosuggest API.

![Lista del cuadro de búsqueda desplegable de AutoSuggest](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Cuando un usuario selecciona una sugerencia en la lista desplegable, puede usarla para comenzar la búsqueda con una de las Bing Search APIs o ir directamente a la página de resultados de búsqueda de Bing.

## <a name="next-steps"></a>Pasos siguientes

Para comenzar rápidamente con su primera solicitud, consulte [Making Your First Query](quickstarts/csharp.md) (Realizar su primera consulta).

Familiarícese con la referencia de [Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). La referencia contiene la lista de puntos de conexión, encabezados y parámetros de consulta que se usan para solicitar términos de consulta sugeridos y las definiciones de los objetos de la respuesta.

Aprenda a realizar búsquedas en la web mediante [Bing Web Search API](../bing-web-search/search-the-web.md).

No olvide leer los [Requisitos de visualización y uso de Bing](./useanddisplayrequirements.md) para evitar infringir alguna de las reglas sobre el uso de los resultados de búsqueda.
