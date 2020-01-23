---
title: Enriquecimiento incremental (versión preliminar)
titleSuffix: Azure Cognitive Search
description: Almacene en caché contenido intermedio y cambios incrementales de la canalización de enriquecimiento con IA en Azure Storage para conservar las inversiones en los documentos procesados existentes. Esta característica actualmente está en su versión preliminar pública.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 285b3608bc57d88ca2e81ed14355923436ed9d8d
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028515"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introducción al enriquecimiento incremental y al almacenamiento en caché en Azure Cognitive Search

> [!IMPORTANT] 
> El enriquecimiento incremental se encuentra actualmente en versión preliminar pública. Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). En la [API REST, versión 2019-05-06-Preview](search-api-preview.md) se proporciona esta característica. Por el momento, no hay compatibilidad con el portal ni con .NET SDK.

El enriquecimiento incremental agrega almacenamiento en caché y disponibilidad de estados a una canalización de enriquecimiento, para conservar la inversión en la salida existente, al mismo tiempo que solo se cambian los documentos afectados por una modificación determinada. Esto no solo permite conservar la inversión monetaria en el procesamiento (en concreto, OCR y procesamiento de imágenes) sino que también ofrece un sistema más eficaz. Cuando las estructuras y el contenido se almacenan en la caché, un indexador puede determinar qué aptitudes han cambiado y ejecutar solo aquellas que se han modificado, así como cualquier aptitud dependiente de bajada. 

## <a name="indexer-cache"></a>Caché de indexador

El enriquecimiento incremental agrega una caché a la canalización de enriquecimiento. El indizador almacena en caché los resultados del descifrado de documentos más las salidas de todas las aptitudes de todos los documentos. Cuando se actualiza un conjunto de aptitudes, solo se vuelven a ejecutar las aptitudes modificadas o descendentes. Los resultados actualizados se escriben en la caché y el documento se actualiza en el índice de búsqueda o el almacén de conocimiento.

Físicamente, la caché se almacena en un contenedor de blobs en la cuenta de Azure Storage. Todos los índices de un servicio de búsqueda pueden compartir la misma cuenta de almacenamiento para la caché del indexador. A cada indizador se le asigna un identificador de caché único e inmutable al contenedor que usa.

## <a name="cache-configuration"></a>Configuración de la caché

Para empezar a beneficiarse del enriquecimiento incremental, tendrá que establecer la propiedad `cache` en el indizador. En el ejemplo siguiente se muestra un indexador con el almacenamiento en caché habilitado. En las secciones siguientes se describen partes específicas de esta configuración. Para más información, vea [Configuración del enriquecimiento incremental](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Para establecer esta propiedad en un indizador existente, tendrá que restablecer y volver a ejecutar el indizador, lo que hará que todos los documentos del origen de datos se vuelvan a procesar. Este paso es necesario para eliminar los documentos enriquecidos por versiones anteriores del conjunto de aptitudes. 

## <a name="cache-management"></a>Administración de la memoria caché

El indexador administra el ciclo de vida de la caché. Si la propiedad `cache` del indizador está establecida en NULL o la cadena de conexión ha cambiado, se eliminará la caché existente en la siguiente ejecución del indizador. El ciclo de vida de la caché también está asociado al ciclo de vida del indexador. Si se elimina un indexador, también se elimina la caché asociada.

Aunque el enriquecimiento incremental está diseñado para detectar y responder a los cambios sin intervención por su parte, existen parámetros que puede usar para invalidar los comportamientos predeterminados:

+ Priorizar nuevos documentos
+ Omitir comprobaciones de conjuntos de aptitudes
+ Omitir comprobaciones de origen de datos
+ Forzar la evaluación de conjuntos de aptitudes

### <a name="prioritize-new-documents"></a>Priorizar nuevos documentos

Establezca la propiedad `enableReprocessing` para controlar el procesamiento de los documentos entrantes ya representados en la caché. Cuando es `true` (valor predeterminado), los documentos que ya están en la caché se vuelven a procesar cuando se vuelve a ejecutar el indizador, siempre que la actualización de las aptitudes afecta a ese documento. 

Cuando es `false`, los documentos existentes no se vuelven a procesar, con lo que se prioriza de forma eficaz el contenido nuevo y entrante sobre el existente. Solo debe establecer `enableReprocessing` en `false` de forma temporal. Para garantizar la coherencia en el corpus, `enableReprocessing` debe ser `true` la mayor parte del tiempo, para asegurarse de que todos los documentos, tanto nuevos como existentes, sean válidos según la definición del conjunto de aptitudes actual.

### <a name="bypass-skillset-evaluation"></a>Omitir la evaluación del conjunto de aptitudes

La modificación y el reprocesamiento de un conjunto de aptitudes suelen estar relacionados. Pero algunos cambios de un conjunto de aptitudes no deben dar como resultado el reprocesamiento (por ejemplo, la implementación de una aptitud personalizada en una ubicación nueva o con una clave de acceso nueva). Lo más probable es que se trate de modificaciones periféricas, sin un impacto real en la sustancia del propio conjunto de aptitudes. 

Si sabe que un cambio en el conjunto de aptitudes es superficial, debe establecer el parámetro `disableCacheReprocessingChangeDetection` en `true` para invalidar la evaluación de conjuntos de aptitudes:

1. Llame a Update Skillset y modifique la definición del conjunto de aptitudes.
1. Anexe el parámetro `disableCacheReprocessingChangeDetection=true` en la solicitud.
1. Envíe el cambio.

Al establecer este parámetro se garantiza que solo se confirman las actualizaciones de la definición del conjunto de aptitudes y que el cambio no se evalúa para los efectos en el corpus existente.

En el ejemplo siguiente se muestra una solicitud Update Skillset con el parámetro:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Omitir comprobaciones de validación de origen de datos

La mayoría de los cambios en una definición de origen de datos invalidarán la caché. Pero para los escenarios en los que sabe que un cambio no debe invalidar la caché, como cambiar una cadena de conexión o rotar la clave en la cuenta de almacenamiento, anexe el parámetro `ignoreResetRequirement` en la actualización del origen de datos. Establecer este parámetro en `true` permite que se realice la confirmación, sin desencadenar una condición de restablecimiento que daría lugar a que todos los objetos se volvieran a generar y se rellenaran desde el principio.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Forzar la evaluación de conjuntos de aptitudes

El propósito de la caché es evitar procesamientos innecesarios, pero imagine que realiza un cambio en una aptitud que el indizador no detecta (por ejemplo, cambiar un elemento del código externo, como una aptitud personalizada).

En este caso, puede usar [Reset Skills](preview-api-resetskills.md) para forzar el reprocesamiento de una aptitud determinada, incluidos los conocimientos de nivel inferior que tengan una dependencia en la salida de esa aptitud. Esta API acepta una solicitud POST con una lista de aptitudes que se deben invalidar y marcar para volver a procesarse. Después de Reset Skills, ejecute el indizador para invocar la canalización.

## <a name="change-detection"></a>Detección de cambios

Una vez que se ha habilitado la caché, el indizador evalúa los cambios en la composición de la canalización para determinar qué contenido se puede reusar y cuál se debe volver a procesar. En esta sección se enumeran los cambios que invalidan la caché directamente, seguidos de los que desencadenan el procesamiento incremental. 

### <a name="changes-that-invalidate-the-cache"></a>Cambios que invalidan la caché

Un cambio invalidador es aquél en el que la caché completa deja de ser válida. Un ejemplo de cambio invalidador es en el que se actualiza el origen de datos. Esta es la lista completa de cambios que invalidarían la memoria caché:

* Cambiar al tipo de origen de datos
* Cambiar al contenedor del origen de datos
* Credenciales del origen de datos
* Directiva de detección de cambios en el origen de datos
* Directiva de detección de eliminaciones en el origen de datos
* Asignaciones de campos del indexador
* Parámetros de un indexador
    * Modo de análisis
    * Extensiones de nombre de archivo excluidas
    * Extensiones de nombre de archivo indexadas
    * Metadatos de almacenamiento de índice solo para documentos de gran tamaño
    * Encabezados de texto delimitado
    * Delimitador de texto delimitado
    * Raíz del documento
    * Acción de imagen (cambios en la forma en que se extraen las imágenes)

### <a name="changes-that-trigger-incremental-processing"></a>Cambios que desencadenan el procesamiento incremental

El procesamiento incremental evalúa la definición del conjunto de aptitudes y determina qué aptitudes se deben volver a ejecutar, con lo que se actualizan selectivamente las partes afectadas del árbol del documento. Esta es la lista completa de cambios que dan como resultado el enriquecimiento incremental:

* La aptitud del conjunto de aptitudes tiene un tipo diferente. El tipo de oData de la aptitud está actualizado
* Los valores predeterminados de los parámetros específicos de la aptitud actualizados, como la dirección URL, u otros parámetros
* Cambios en las salidas de la aptitud, la aptitud devuelve salidas adicionales o diferentes
* Las actualizaciones de aptitudes resultantes tienen otra ascendencia, el encadenamiento de aptitudes ha cambiado, es decir, las entradas de las aptitudes
* Cualquier invalidación de aptitudes ascendente, si se actualiza una aptitud que proporcione una entrada a esta aptitud
* Actualizaciones de la ubicación de la proyección del almacén de información, lo que provoca que se vuelvan a proyectar los documentos
* Cambios en las proyecciones del almacén de información, lo que provoca que se vuelvan a proyectar los documentos
* Las asignaciones de campos de salida cambiadas en un indexador provocan que se vuelvan a proyectos los documentos en el índice

## <a name="api-reference-content-for-incremental-enrichment"></a>Contenido de referencia de API para el enriquecimiento incremental

REST `api-version=2019-05-06-Preview` proporciona las API para el enriquecimiento incremental, con adiciones a indizadores, conjuntos de aptitudes y orígenes de datos. La [documentación de referencia oficial](https://docs.microsoft.com/rest/api/searchservice/) es para las API de disponibilidad general y no cubre las características en vista previa. En la sección siguiente se proporciona el contenido de referencia para las API afectadas.

Puede encontrar información de uso y ejemplos en [Configuración del almacenamiento en caché para el enriquecimiento incremental](search-howto-incremental-index.md).

### <a name="indexers"></a>Indexadores

Las opciones [Crear indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) y [Actualizar indexador](https://docs.microsoft.com/rest/api/searchservice/update-indexer) ahora exponen nuevas propiedades relacionadas con la caché:

+ `StorageAccountConnectionString`: la cadena de conexión a la cuenta de almacenamiento que se usará para almacenar en caché los resultados intermedios.

+ `EnableReprocessing`: Establecido en `true` de forma predeterminada, si se establece en `false`, los documentos se seguirán escribiendo en la memoria caché, pero no se volverá a procesar ningún documento existente en función de los datos de la caché.

+ `ID` (solo lectura): `ID` es el identificador del contenedor de la cuenta de almacenamiento `annotationCache` que se usará como caché para este indexador. Esta caché será única para este indexador y, si el indexador se elimina y se vuelve a crear con el mismo nombre, se volverá a generar `ID`. `ID` no se puede establecer, siempre lo genera el servicio.

### <a name="skillsets"></a>Conjuntos de aptitudes

+ [Update Skillset](https://docs.microsoft.com/rest/api/searchservice/update-skillset) admite un nuevo parámetro en la solicitud, `disableCacheReprocessingChangeDetection`, que se debe establecer en `true` cuando no se quieren actualizar los documentos existentes en función de la acción actual.

+ [Reset Skills](preview-api-resetskills.md) es una operación nueva que se usa para invalidar un conjunto de aptitudes.

### <a name="datasources"></a>Orígenes de datos

+ Algunos indizadores recuperan datos a través de consultas. En cuanto a las consultas que recuperan datos, [Update Data Source](https://docs.microsoft.com/rest/api/searchservice/update-data-source) admite un nuevo parámetro en una consulta `ignoreResetRequirement`, que se tiene que establecer en `true` si la acción de actualización no debe invalidar la caché.

Use `ignoreResetRequirement` con moderación, ya que podría dar lugar a incoherencias no intencionadas en los datos que no se detectarían fácilmente.

## <a name="next-steps"></a>Pasos siguientes

El enriquecimiento incremental es una característica eficaz que extiende el seguimiento de cambios en conjuntos de aptitudes y el enriquecimiento con IA. Cuando los conjuntos de aptitudes evolucionan, el enriquecimiento incremental garantiza que se realiza el menor trabajo posible mientras se logra la coherencia final de los documentos.

Para empezar, agregue una caché a un indizador existente, o bien agregue la caché al definir un indizador nuevo.

> [!div class="nextstepaction"]
> [Configuración del almacenamiento en caché para el enriquecimiento incremental](search-howto-incremental-index.md)
