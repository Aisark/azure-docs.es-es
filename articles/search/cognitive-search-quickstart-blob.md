---
title: 'Inicio rápido: Creación de un conjunto de aptitudes en Azure Portal'
titleSuffix: Azure Cognitive Search
description: En este inicio rápido del portal, aprenda a usar el Asistente para importar datos para agregar aptitudes cognitivas a una canalización de indexación en Azure Cognitive Search. Las aptitudes incluyen el reconocimiento óptico de caracteres (OCR) y el procesamiento de lenguaje natural.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 5e891627b337a0a3a15d0ebfa2b9cc95f27feca4
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533115"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Inicio rápido: Creación de un conjunto de aptitudes cognitivas de Azure Cognitive Search en Azure Portal

Un conjunto de aptitudes es una característica de inteligencia artificial que extrae la información y la estructura de grandes archivos de texto no diferenciados o de imágenes, y permite que se puedan indexar y realizar búsquedas con consultas de búsqueda de texto completo en Azure Cognitive Search. 

En este inicio rápido, combinará servicios y datos en la nube de Azure para crear el conjunto de aptitudes. Una vez que todo esté en orden, ejecutará el Asistente para la **importación de datos** en el portal para extraer toda la información al mismo tiempo. El resultado final es un índice en el que se pueden realizar búsquedas y que se ha rellenado con los datos creados con el procesamiento de inteligencia artificial que puede consultar en el portal ([Explorador de búsqueda](search-explorer.md)).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-services-and-load-data"></a>Creación de servicios y carga de datos

En este inicio rápido, se usa Azure Cognitive Search, Azure Blob Storage y [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) para la inteligencia artificial. 

Dado que la carga de trabajo es tan pequeña, Cognitive Services se aprovecha en segundo plano para proporcionar el procesamiento gratuito de hasta 20 transacciones al día, cuando se invoca desde Azure Cognitive Search. Siempre que use los datos de ejemplo que se proporcionan, puede omitir la creación o asociación de un recurso de Cognitive Services.

1. [Descargue los datos de ejemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) que están formados por un pequeño conjunto de archivos de diferentes tipos. Descomprima los archivos.

1. [Cree una cuenta de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) o [busque una cuenta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Elija la misma región de Azure Cognitive Search. Elija el tipo de cuenta StorageV2 (de uso general V2) si desea probar la característica almacén de conocimiento más adelante, en otro tutorial. De lo contrario, elija cualquier tipo.

1. Abra las páginas de Blob service y cree un contenedor. Puede usar el nivel de acceso público predeterminado. 

1. En el contenedor, haga clic en **Cargar** para cargar los archivos de ejemplo que descargó en el primer paso. Tenga en cuenta que dispone de una amplia gama de tipos de contenido, como imágenes y archivos de aplicación, en los que no se pueden realizar búsquedas de texto completo en sus formatos nativos.

   ![Archivos de origen en Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Cree un servicio de Azure Cognitive Search](search-create-service-portal.md) o [busque un servicio existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Puede usar un servicio gratuito para este inicio rápido.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

Ahora ya está preparado para continuar con el Asistente para la importación de datos.

## <a name="run-the-import-data-wizard"></a>Ejecutar el Asistente para la importación de datos

En la página de información general del servicio de búsqueda, haga clic en **Importar datos** en la barra de comandos para configurar el enriquecimiento cognitivo en cuatro pasos.

  ![Comando de importación de datos](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos

1. En **Conectarse a los datos**, elija **Azure Blob Storage** y seleccione la cuenta de Storage y el contenedor que creó. Asigne un nombre al origen de datos y use los valores predeterminados para el resto. 

   ![Configuración de blobs de Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Continúe en la siguiente página.

### <a name="step-2-add-cognitive-skills"></a>Paso 2: Agregar conocimientos cognitivos

A continuación, agregue aptitudes cognitivas para invocar el procesamiento de lenguaje natural. Los datos de ejemplo se componen de 12 archivos, por lo que la cobertura gratuita de 20 transacciones de Cognitive Services es suficiente para este inicio rápido. Dado que no usamos OCR, solo aquellos archivos que no sean de imagen se contarán, descifrarán y emplearán en este proceso.

1. En este inicio rápido, se usará el recurso **Free** de Cognitive Services.

   ![Adjuntar Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Expanda **Agregar aptitudes** y seleccione las aptitudes que realizan el procesamiento de lenguaje natural. En esta guía de inicio rápido, elija el reconocimiento de entidades para los contactos, las organizaciones y las ubicaciones.

   ![Adjuntar Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

1. Acepte el campo de origen predeterminado: `content`. Esto puede parecer un objetivo pequeño, pero para los blobs de Azure, el campo `content` contiene la mayor parte del documento de blobs (por ejemplo, un documento de Word o unas diapositivas de PowerPoint), lo cual hace que resulte un buen candidato.

1. Continúe en la siguiente página.

> [!NOTE]
> Las aptitudes de procesamiento de lenguaje natural operan en el contenido de texto que se encuentra en el conjunto de datos de ejemplo. Puesto que no seleccionamos ninguna opción de OCR, los archivos JPEG y PNG que se encontraron en el conjunto de datos de ejemplo no se procesarán en este artículo de inicio rápido. 

### <a name="step-3-configure-the-index"></a>Paso 3: Configuración del índice

En Azure Cognitive Search, un índice contiene el contenido en el que se pueden realizar búsquedas y el Asistente para la **importación de datos** puede crear normalmente el esquema mediante el muestreo del origen de datos. En este paso, repase el esquema generado y revise la configuración. A continuación se ofrece el esquema predeterminado que se creó para el conjunto de datos de blob de demostración.

En esta guía de inicio rápido, el asistente realiza un trabajo remarcable a la hora de configurar valores predeterminados razonables: 

+ El nombre predeterminado es *azureblob-index* basado en el tipo de origen de datos. 

+ Los campos predeterminados se basan en el campo de datos de origen original (`content`), además de los campos de salida (`people`, `organizations` y `locations`) creados por las aptitudes cognitivas. Los tipos de datos predeterminados se deducen del muestreo de datos y metadatos.

+ La clave de documento predeterminada es *metadata_storage_path* (se ha seleccionado porque el campo contiene valores únicos).

+ Los atributos predeterminados son **Retrievable** (Recuperable) y **Searchable** (Permite búsquedas) para estos campos. El atributo **Searchable** indica que se puede buscar un campo. El atributo **Retrievable** indica que un valor puede aparecer en los resultados. El asistente da por supuesto que desea que estos campos se puedan recuperar y permitan búsquedas porque los creó a través de un conjunto de aptitudes.

  ![Campos de índice](media/cognitive-search-quickstart-blob/index-fields.png)

Observe el tachado y el signo de interrogación en el atributo **Retrievable** del campo `content`. En el caso de los documentos blob con mucho texto, el campo `content` contiene la mayor parte del archivo, posiblemente con miles de líneas. Si tiene que pasar el contenido del archivo al código de cliente, asegúrese de que **Retrievable** sigue seleccionado. De lo contrario, considere la posibilidad de borrar este atributo en `content` si los elementos extraídos (`people`, `organizations` y `locations`) son suficientes para sus fines.

Marcar un campo como **Retrievable** no significa que el campo *debe* esté presente en los resultados de búsqueda. Puede controlar con precisión la composición de los resultados de búsqueda si usa el parámetro de consulta **$select** para especificar qué campos desea incluir. En el caso de campos con mucho texto como `content`, el parámetro **$select** es la solución para proporcionar resultados de búsqueda fáciles de administrar a los usuarios de la aplicación, al tiempo que se asegura de que el código de cliente tenga acceso a toda la información que necesita a través del atributo **Retrievable**.
  
Continúe en la siguiente página.

### <a name="step-4-configure-the-indexer"></a>Paso 4: Configurar el indexador

El indexador es un recurso de alto nivel que controla el proceso de indexación. Asimismo, especifica el nombre del origen de datos, un índice de destino y la frecuencia de ejecución. El Asistente para la **importación de datos** crea varios objetos y entre ellos hay siempre un indexador que se puede ejecutar repetidamente.

1. En la página **Indizador**, puede aceptar el nombre predeterminado y hacer clic en la opción de programación **Una vez** para ejecutarlo inmediatamente. 

   ![Definición del indexador](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Haga clic en **Enviar** para crear y ejecutar simultáneamente el indexador.

## <a name="monitor-status"></a>Supervisión de estado

La indexación cognitiva de aptitudes tarda más en completarse que la indexación típica basada en texto. Para supervisar el progreso, vaya a la página de información general y haga clic en **Indizadores** en medio de la página.

La advertencia se produce porque hay archivos de imagen JPG y PNG en el origen de datos y se ha omitido la aptitud de OCR de esta canalización. También encontrará notificaciones de truncamiento. La extracción está limitada a 32 000 caracteres en el nivel Gratis.

  ![Notificación de Azure Cognitive Search](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Los procesos de enriquecimiento e indexación pueden tardar cierto tiempo y, por ello, se recomienda usar conjuntos de datos más pequeños para poder realizar una exploración temprana. 

En Azure Portal, puede también supervisar el registro de actividad de notificaciones para obtener un vínculo de estado **Notificación de Azure Cognitive Search** en el que se pueda hacer clic. La ejecución puede tardar varios minutos en completarse.

## <a name="query-in-search-explorer"></a>Consulta en el Explorador de búsqueda

Después de crear un índice, puede enviar consultas para devolver documentos desde el índice. En el portal, utilice el **Explorador de búsqueda** para ejecutar consultas y ver los resultados. 

1. En la página del panel del servicio de búsqueda, haga clic en el **Explorador de búsqueda** en la barra de comandos.

1. Seleccione **Cambiar índice** en la parte superior para seleccionar el índice que haya creado.

1. Escriba una cadena de búsqueda para consultar el índice como, por ejemplo, `search=Microsoft&searchFields=Organizations`.

Los resultados se devuelven en formato JSON, que suele ser detallado y difícil de leer, especialmente si se trata de documentos de gran tamaño que se crean en los blobs de Azure. Si no puede examinar los resultados con facilidad, utilice CTRL-F para buscar dentro de los documentos. Para esta consulta, puede buscar términos específicos en JSON. 

CTRL-F también puede ayudarle a determinar cuántos documentos hay en un determinado conjunto de resultados. En cuanto a los blobs de Azure, el portal elige "metadata_storage_path" como clave, ya que cada valor es único en el documento. Use CTRL-F y busque "metadata_storage_path" para obtener un recuento de los documentos. 

  ![Ejemplo del Explorador de búsquedas](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Puntos clave

Ahora ha creado su primer conjunto de aptitudes y ha aprendido conceptos importantes útiles para crear prototipos de una solución de búsqueda enriquecida mediante sus propios datos.

Algunos conceptos clave que esperamos que haya tenido en cuenta incluyen la dependencia de los orígenes de datos de Azure. Un conjunto de aptitudes está enlazado a un indexador y los indexadores son de Azure y específicos del origen. Aunque esta guía de inicio rápido usa Azure Blob Storage, también se pueden usar otros orígenes de datos de Azure. Para más información, consulte [Indexadores de Azure Cognitive Search](search-indexer-overview.md).

Otro concepto importante es que las aptitudes operan sobre los campos de entrada. En el portal, debe elegir un solo campo de origen para todas las aptitudes. En el código, las entradas pueden ser otros campos o la salida de una aptitud ascendente.

La salida se dirige a un índice de búsqueda y hay una asignación entre los pares nombre y valor que se crearon durante la indexación y los campos individuales del índice. De forma interna, el portal establece las [anotaciones ](cognitive-search-concept-annotations-syntax.md) y define un [conjunto de aptitudes](cognitive-search-defining-skillset.md) que establece el orden de las operaciones y el flujo general. Estos pasos están ocultos en el portal, pero recuerde que estos conceptos serán importantes cuando comience a escribir el código.

Por último, aprendió que se puede comprobar el contenido consultando el índice. Al final, lo que proporciona Azure Cognitive Search es un índice de búsqueda que puede consultar mediante la [sintaxis de consulta simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) o [totalmente extendida](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Un índice que contenga campos enriquecidos es como cualquier otro. Si desea incorporar [analizadores personalizados](search-analyzers.md) o estándar, [perfiles de puntuación](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [sinónimos](search-synonyms.md), [filtros con facetas](search-filters-facets.md), búsquedas geográficas o cualquier otra característica de Azure Cognitive Search, puede hacerlo cuando quiera.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando trabaje con su propia suscripción, es una buena idea al final de un proyecto identificar si todavía se necesitan los recursos que ha creado. Los recursos que se dejan en ejecución pueden costarle mucho dinero. Puede eliminar los recursos de forma individual o eliminar el grupo de recursos para eliminar todo el conjunto de recursos.

Puede encontrar y administrar recursos en el portal, mediante el vínculo **Todos los recursos** o **Grupos de recursos** en el panel de navegación izquierdo.

Si está usando un servicio gratuito, recuerde que está limitado a tres índices, indexadores y orígenes de datos. Puede eliminar elementos individuales en el portal para mantenerse por debajo del límite. 

> [!Tip]
> Si desea repetir este ejercicio o probar otro tutorial de enriquecimiento con inteligencia artificial, elimine el indexador del portal. La eliminación del indexador restablece el contador de transacciones diarias gratis a cero para el procesamiento de Cognitive Services.

## <a name="next-steps"></a>Pasos siguientes

Para crear estos conjuntos de aptitudes, puede usar el portal, el SDK de .NET o la API REST. Para ampliar sus conocimientos, pruebe la API REST con Postman y más datos de ejemplo.

> [!div class="nextstepaction"]
> [Tutorial: Adición de una estructura a "contenido no estructurado" con enriquecimiento de inteligencia artificial](cognitive-search-tutorial-blob.md)