---
title: Sugerencia de términos de búsqueda con Bing Autosuggest API
titlesuffix: Azure Cognitive Services
description: Aprenda a usar Bing Autosuggest API.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 293dcaadfc20116455983b3fc0069f9e9df3f843
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010555"
---
# <a name="suggesting-query-terms"></a>Sugerencia de términos de consulta

Generalmente, llamaría a Bing Autosuggest API cada vez que un usuario escribiera un carácter en el cuadro de búsqueda de la aplicación. La integridad de la cadena de consulta afecta a la relevancia de los términos de la consulta sugeridos que devuelve la API. Cuanto más completa sea la cadena de consulta, más apropiada será la lista de términos de consulta sugeridos. Por ejemplo, las sugerencias que puede devolver la API para `s` es probable que tengan menos relevancia que las consultas que devuelve para `sailing dinghies`.

## <a name="example-request"></a>Solicitud de ejemplo

En el ejemplo siguiente se muestra una solicitud que devuelve las cadenas de consulta sugeridas para *navegar*. Recuerde codificar como dirección URL el término de consulta parcial del usuario al establecer el parámetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query). Por ejemplo, si el usuario escribe *sailing les*, establezca `q` en `sailing+les` o `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

La respuesta siguiente contiene una lista de objetos [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) que contienen los términos de consulta sugeridos.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Uso de términos de consulta sugeridos

Cada sugerencia incluye un campo `displayText`, `query` y `url`. El campo `displayText` contiene la consulta sugerida que usa para rellenar la lista desplegable del cuadro de búsqueda. Debe mostrar todas las sugerencias que incluye la respuesta, y en el orden especificado.

El ejemplo siguiente muestra un cuadro de búsqueda desplegable con los términos de consulta sugeridos de Bing Autosuggest API.

![Lista del cuadro de búsqueda desplegable de AutoSuggest](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Si el usuario selecciona una consulta sugerida en la lista desplegable, puede usar la cadena de consulta del campo `query` para llamar a [Bing Web Search API](../../bing-web-search/search-the-web.md) y ver los resultados por sí mismo. O bien, puede usar la dirección URL del campo `url` para enviar al usuario a la página de resultados de la búsqueda de Bing.

## <a name="next-steps"></a>Pasos siguientes

* [Qué es Bing Autosuggest API](../get-suggested-search-terms.md)