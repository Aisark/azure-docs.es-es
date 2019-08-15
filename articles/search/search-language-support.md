---
title: 'Indexación multilingüe para consultas de búsqueda que no están en inglés: Azure Search'
description: Azure Search admite 56 idiomas y aprovecha los analizadores de idiomas Lucene y la tecnología de procesamiento de lenguaje natural de Microsoft.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: jlembicz
ms.openlocfilehash: 5383ad44f665ce809772143e23817932c3e2b7e6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883884"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-search"></a>Cómo crear un índice para varios idiomas en Azure Search

Los índices pueden incluir campos con contenido en varios idiomas, por ejemplo, la creación de campos individuales para cadenas específicas del idioma. Para obtener los mejores resultados durante la indexación y la consulta, asigne un analizador de idioma que proporcione las reglas lingüísticas adecuadas. 

Azure Search ofrece una amplia selección de analizadores de idioma de Lucene y Microsoft que se pueden asignar a campos individuales mediante la propiedad Analyzer. También puede especificar un analizador de idioma en el portal, como se describe en este artículo.

## <a name="add-analyzers-to-fields"></a>Adición de analizadores a campos

Los analizadores de idioma se especifican cuando se crea un campo. Para agregar un analizador a una definición de campo existente, es necesario sobrescribir (y volver a cargar) el índice, o bien crear un campo idéntico al original, pero con una asignación de analizador. Después, según le convenga, puede eliminar el campo sin usar.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y busque su servicio de búsqueda.
1. Haga clic en **Agregar un índice** en la barra de comandos de la parte superior del panel de servicio para iniciar un nuevo índice, o abra un índice existente para establecer un analizador de nuevos campos que se va a agregar a un índice existente.
1. Para iniciar una definición de campo, proporcione un nombre.
1. Seleccione el tipo de datos Edm.String. Solo se puede realizar una búsqueda de texto completo en los campos de cadena.
1. Establezca el atributo **Searchable** para habilitar la propiedad Analyzer. Un campo debe estar basado en texto para poder usar un analizador de idioma.
1. Elija uno de los analizadores disponibles. 

![Asignación de analizadores de idioma durante la definición del campo](media/search-language-support/select-analyzer.png "Assign language analyzers during field definition")

De forma predeterminada, todos los campos en los que se puede buscar usan el [analizador Lucene estándar](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), que no depende del idioma. Para ver la lista completa de los analizadores compatibles, consulte [Incorporación de analizadores de idiomas a un índice de Azure Search](index-add-language-analyzers.md).

Los analizadores están diseñados para usarse tal cual en el portal. Si necesita algún tipo de personalización o configuración específica para los filtros y los tokenizadores, debe [crear un analizador personalizado](index-add-custom-analyzers.md) en el código. El portal no admite la selección ni la configuración de analizadores personalizados.

## <a name="query-language-specific-fields"></a>Consulta de campos específicos del idioma

Una vez que el analizador de idioma está activado para un campo, se usará con cada solicitud de búsqueda e indexación para ese campo. Cuando se emite una consulta en varios campos con diferentes analizadores, los analizadores asignados a cada campo procesarán la consulta de manera independiente.

Si se conoce el idioma del agente que emite una consulta, una solicitud de búsqueda se puede aplicar a un campo específico con el parámetro de consulta **searchFields** . La siguiente consulta solo se emitirá en relación a la descripción en polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Puede consultar el índice desde el portal con el [**Explorador de búsqueda**](search-explorer.md) para pegar una consulta similar a la que se ha mostrado anteriormente.

## <a name="boost-language-specific-fields"></a>Priorización de campos específicos del idioma

A veces se desconoce el idioma del agente que emite una consulta, en cuyo caso la consulta puede emitir en todos los campos al mismo tiempo. Si es necesario, se pueden definir una preferencia de resultados en un determinado idioma mediante [perfiles de puntuación](index-add-scoring-profiles.md). En el ejemplo siguiente, las coincidencias encontradas en la descripción en inglés tendrán una puntuación mayor que las coincidencias en polaco y francés:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Pasos siguientes

Si es desarrollador de .NET, tenga en cuenta que puede configurar los analizadores de idioma mediante el [SDK de Azure Search para .NET](https://www.nuget.org/packages/Microsoft.Azure.Search) y la propiedad [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet). 