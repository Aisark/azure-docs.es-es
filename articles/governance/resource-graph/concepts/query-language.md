---
title: Descripción del lenguaje de consultas
description: Describe los operadores de Kusto disponibles y las funciones que puede usar con el gráfico de recursos de Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 618445c5b792317d4de4b668e7ea1a186707007c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317924"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Información del lenguaje de consulta de Azure Resource Graph

El lenguaje de consulta de Azure Resource Graph admite numerosos operadores y funciones. Cada uno de ellos trabaja y opera en función de [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md).

La mejor manera de obtener información sobre el lenguaje de consultas que Resource Graph utiliza es empezar con la documentación del [lenguaje de consultas](/azure/kusto/query/index) de Azure Data Explorer. En esta documentación, se explica cómo está estructurado el lenguaje y cómo trabajan juntos los distintos operadores y funciones compatibles.

## <a name="supported-tabular-operators"></a>Operadores tabulares compatibles

Esta es la lista de los operadores tabulares compatibles en Resource Graph:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funciones compatibles

Esta es la lista de las funciones compatibles en Resource Graph:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>Pasos siguientes

- Ver el lenguaje en uso en el artículo de[consultas básicas](../samples/starter.md)
- Consultar usos avanzados en el artículo de [consultas avanzadas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)