---
title: Referencia de la función search.score de OData
titleSuffix: Azure Cognitive Search
description: Documentación de referencia y sintaxis para usar la función search.score en las consultas de Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113138"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Función `search.score` de OData en Azure Cognitive Search

Cuando se envía una consulta a Azure Cognitive Search sin el parámetro [ **$orderby**](search-query-odata-orderby.md), los resultados que se devuelven se clasificarán en orden descendente por puntuación de relevancia. Incluso cuando se usa **$orderby**, de forma predeterminada se usará la puntuación de relevancia para resolver los empates. Pero en ocasiones resulta útil usar la puntuación de relevancia como un criterio de ordenación inicial y cualquier otro criterio como el factor de desempate. La función `search.score` permite hacer esto.

## <a name="syntax"></a>Sintaxis

La sintaxis de `search.score` en **$orderby** es `search.score()`. La función `search.score` no toma ningún parámetro. Se puede usar con el especificador de criterio de ordenación `asc` o `desc`, al igual que cualquier otra cláusula del parámetro **$orderby**. Puede aparecer en cualquier lugar de la lista de criterios de ordenación.

## <a name="example"></a>Ejemplo

Clasificar hoteles en orden descendente por `search.score` y `rating`, y después en orden ascendente por distancia de las coordenadas dadas de manera que, entre dos hoteles con valoraciones idénticas, el más cercano aparezca primero en la lista:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Pasos siguientes  

- [Información general sobre el lenguaje de expresiones OData para Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referencia de sintaxis de expresiones OData para Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Búsqueda de documentos &#40;API REST de Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
