---
title: 'Azure Cosmos DB: API, SDK y recursos de .NET Core para SQL'
description: Aprenda todo lo necesario sobre el SDK y la API de .NET Core para SQL como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios realizados de una versión a otra del SDK para .NET Core de Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: de712d63fa2315a85c6c0eaf392d9c17758e4999
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226181"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>SDK de .NET Core de Azure Cosmos DB para API de SQL: Notas de la versión y recursos
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Fuente de cambios de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor: .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor: Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Descarga del SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Documentación de la API**|[Documentación de referencia de API de .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Muestras**|[Ejemplos de código de .NET](sql-api-dotnet-samples.md)|
|**Primeros pasos**|[Introducción a .NET de Azure Cosmos DB](sql-api-sdk-dotnet.md)|
|**Tutorial de la aplicación web**|[Desarrollo de aplicaciones web con Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Plataforma admitida actualmente**|[.NET Standard 1.6 y .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Notas de la versión

> [!NOTE]
> Si usa .NET Core, consulte la versión 3. x más reciente del [SDK de .NET](sql-api-sdk-dotnet-standard.md), que tiene como destino .NET Standard. 

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Se corrige la condición de carrera de seguimiento para las consultas que causaron páginas vacías.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* La versión de System.Net.Http del SDK coincide con lo definido en el paquete NuGet
* Mayor tamaño de precisión decimal para consultas de LINQ.
* Incorporación de nuevas clases CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType y PartitionKeyDefinitionVersion
* Incorporación de TimeToLivePropertyPath a DocumentCollection
* Incorporación de CompositeIndexes y SpatialIndexes a IndexPolicy
* Incorporación de Version a PartitionKeyDefinition
* Incorporación de None a PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Incorporación de IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection y MaxTcpConnectionsPerEndpoint a ConnectionPolicy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Mejoras de diagnósticos

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Se ha agregado la configuración de variable de entorno "POCOSerializationOnly".

* Se ha quitado DocumentDB.Spatial.Sql.dll y ahora se incluye en Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Mejora en la lógica de reintento durante la conmutación por error para las llamadas de ejecución StoredProcedure.

* DocumentClientEventSource se ha vuelto una base de datos única. 

* Corrección en el tiempo de espera de GatewayAddressCache que no respetaba ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Para el diagnóstico de transporte directo o TCP, se ha agregado TransportException, un tipo de excepción interna del SDK. Cuando aparece en los mensajes de excepción, este tipo imprime información adicional para solucionar problemas de conectividad de cliente.

* Se ha agregado una sobrecarga de constructor nueva que toma un objeto HttpMessageHandler, una pila de controlador HTTP que se usa para enviar solicitudes HttpClient (por ejemplo, HttpClientHandler).

* Se ha corregido un error por el que el encabezado con valores NULL no se controlaba correctamente.

* Se ha mejorado la validación de caché de las colecciones.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* System.Net.Security actualizada a 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Mejoras en el seguimiento de diagnósticos

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Se agregó más resistencia a errores transitorios de solicitud de varias regiones.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Se agregó compatibilidad con escrituras de varias regiones.
* Mejoras en el rendimiento de las consultas entre particiones con TOP y MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Se agregó la compatibilidad con la cancelación de solicitudes.
* Se agregó SetCurrentLocation a ConnectionPolicy, que rellena automáticamente las ubicaciones preferidas según la región.
* Se corrigió el error en las consultas de partición cruzada con Mín./máx. y un filtro que coincide con "ningún documento" en una partición individual.
* Los métodos de DocumentClient ahora tienen paridad con IDocumentClient.
* Se actualizó la pila de transporte TCP directo para reducir el número de conexiones establecidas.
* Se agregó la compatibilidad para TCP de modo directo para clientes que no sean Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Se agregó la compatibilidad con la cancelación de solicitudes.
* Se agregó SetCurrentLocation a ConnectionPolicy, que rellena automáticamente las ubicaciones preferidas según la región.
* Se corrigió el error en las consultas de partición cruzada con Mín./máx. y un filtro que coincide con "ningún documento" en una partición individual.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Los métodos de DocumentClient ahora tienen paridad con IDocumentClient.
* Se actualizó la pila de transporte TCP directo para reducir el número de conexiones establecidas.
* Se agregó la compatibilidad para TCP de modo directo para clientes que no sean Windows.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Se agregó la propiedad ConsistencyLevel a FeedOptions.
* Se agregó JsonSerializerSettings a RequestOptions y FeedOptions.
* Se agregó EnableReadRequestsFallback a ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Se ha corregido KeyNotFoundException para el orden de particiones cruzadas por consultas en casos excepcionales.
* Se ha corregido el error, donde no se ha respetado el atributo JsonProperty de la cláusula select para consultas LINQ.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Se ha corregido un error que se producía en determinadas condiciones de carrera, que daba como resultado errores "Microsoft.Azure.Documents.NotFoundException: la sesión de lectura no está disponible para el token de sesión de entrada" intermitentes cuando se usa el nivel de coherencia de sesión.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Se ha corregido la regresión en la que FeedOptions.MaxItemCount = -1 producía una excepción System.ArithmeticException: el tamaño de la página es negativo.
* Se agregó una nueva función ToString() a QueryMetrics.
* Se expusieron las estadísticas de partición en la lectura de las colecciones.
* Se agregó la propiedad PartitionKey a ChangeFeedOptions.
* Correcciones de errores leves.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Agrega la capacidad de especificar índices únicos para los documentos mediante el uso de la propiedad UniqueKeyPolicy en DocumentCollection.
 * Se ha corregido un error en el cual la configuración personalizada de JsonSerializer no se estaba respetando para algunas consultas y para la ejecución de procedimientos almacenados.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Cambio de personalización de marca de Azure DocumentDB a Azure Cosmos DB en la documentación de referencia de API, la información de metadatos de los ensamblados y el paquete NuGet.
 * Exposición de la información de diagnóstico y la latencia de la respuesta de las solicitudes enviadas con el modo de conexión directa. Los nombres de propiedad son RequestDiagnosticsString y RequestLatency en la clase ResourceResponse.
 * Esta versión del SDK requiere la versión más reciente del emulador de Azure Cosmos DB disponible para su descarga desde https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Se han agregado varias mejoras y correcciones que aumentan la confiabilidad.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Cambios internos relacionados con la compatibilidad con [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Se agregó compatibilidad para PartitionKeyRangeId como un FeedOption para definir el ámbito de los resultados de la consulta en un valor de intervalo de claves de partición específico.
* Se agregó compatibilidad para StartTime como un ChangeFeedOption para empezar la búsqueda de los cambios después de ese momento.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Se corrigió un problema en la clase JsonSerializable que podría provocar una excepción de desbordamiento de pila.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Se agregó compatibilidad con la especificación de JsonSerializerSettings personalizado al crear una instancia de [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Compatibilidad con .NET Standard 1.5 como una de las siguientes plataformas de destino.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Se corrigió un problema que afectaba a las máquinas x64 que no admiten instrucciones SSE4 y que producía una excepción SEHException al ejecutar consultas de Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Se agregó compatibilidad con un nuevo nivel de coherencia denominado ConsistentPrefix.
*   Se agregó compatibilidad con métricas de consulta para particiones individuales.
*   Se agregó compatibilidad para limitar el tamaño del token de continuación para las consultas.
*   Se agregó compatibilidad para un seguimiento más detallado de las solicitudes con error.
*   Se hicieron algunas mejoras de rendimiento en el SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Se ha corregido un problema que omitía el valor PartitionKey proporcionado en FeedOptions para consultas de funciones agregadas.
* Se ha corregido un problema en el control transparente de administración de particiones durante la ejecución entre particiones de la consulta Order By.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Se ha corregido un problema que provocaba interbloqueos en algunas de las API asincrónicas cuando se empleaban en el contexto de ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Correcciones para aumentar la resistencia de los SDK frente a la conmutación automática por error en determinadas condiciones.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Se ha corregido un problema que en ocasiones provoca una excepción WebException: No se pudo resolver el nombre remoto.
* Se ha agregado compatibilidad para leer un documento escrito directamente mediante la adición de nuevas sobrecargas a la API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Se agregó compatibilidad con consultas de agregación para LINQ (COUNT, MIN, MAX, SUM y AVG).
* Se corrigió un problema de pérdida de memoria para el objeto ConnectionPolicy causado por el uso del controlador de eventos.
* Se corrigió un problema por el que UpsertAttachmentAsync no funcionaba cuando se usaba el valor ETag.
* Se corrigió un problema por el que la continuación de la consulta order-by en la partición cruzada no funcionaba cuando se ordenaba por un campo de cadena.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG). Consulte [Compatibilidad con agregación](sql-query-aggregates.md).
* Reducción del procesamiento mínimo en las colecciones particionadas de 10 100 RU/s a 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

El SDK de .NET Core para Azure Cosmos DB le permite crear aplicaciones [ASP.NET Core](https://www.asp.net/core) y [.NET Core](https://www.microsoft.com/net/core#windows) rápidas y multiplataforma: Windows, Mac y Linux. La versión más reciente del SDK de .NET Core para Azure Cosmos DB es totalmente compatible con [Xamarin](https://www.xamarin.com) y se usará para compilar aplicaciones que tienen como destino iOS, Android y Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

El SDK de .NET Core para Azure Cosmos DB en versión preliminar le permite crear aplicaciones [ASP.NET Core](https://www.asp.net/core) y [.NET Core](https://www.microsoft.com/net/core#windows) rápidas y multiplataforma: Windows, Mac y Linux.

Incluye paridad de características con la última versión del [SDK de .NET para Azure Cosmos DB](sql-api-sdk-dotnet.md) y admite lo siguiente:
* Todos los [modos de conexión](performance-tips.md#networking): modo de puerta de enlace, TCP directo y HTTPS directo.
* Todos los [niveles de coherencia](consistency-levels.md): Segura, Sesión, Obsolescencia limitada y Posible.
* [Colecciones con particiones](partition-data.md).
* [Cuentas de base de datos de varias regiones y replicación geográfica](distribute-data-globally.md).

Si tiene preguntas relacionadas con este SDK, publíquelas en [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb) o abra una incidencia en el [repositorio de GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [2.4.1](#2.4.1) |20 de junio de 2019 |--- |
| [2.4.0](#2.4.0) |5 de mayo de 2019 |--- |
| [2.3.0](#2.3.0) |4 de abril de 2019 |--- |
| [2.2.3](#2.2.3) |11 de marzo de 2019 |--- |
| [2.2.2](#2.2.2) |6 de febrero de 2019 |--- |
| [2.2.1](#2.2.1) |24 de diciembre de 2018 |--- |
| [2.2.0](#2.2.0) |7 de diciembre de 2018 |--- |
| [2.1.3](#2.1.3) |15 de octubre de 2018 |--- |
| [2.1.2](#2.1.2) |04 de octubre de 2018 |--- |
| [2.1.1](#2.1.1) |27 de septiembre de 2018 |--- |
| [2.1.0](#2.1.0) |21 de septiembre de 2018 |--- |
| [2.0.0](#2.0.0) |07 de septiembre de 2018 |--- |
| [1.9.1](#1.9.1) |9 de marzo de 2018 |--- |
| [1.8.2](#1.8.2) |21 de febrero de 2018 |--- |
| [1.8.1](#1.8.1) |5 de febrero de 2018 |--- |
| [1.7.1](#1.7.1) |16 de noviembre de 2017 |--- |
| [1.7.0](#1.7.0) |10 de noviembre de 2017 |--- |
| [1.6.0](#1.6.0) |17 de octubre de 2017 |--- |
| [1.5.1](#1.5.1) |02 de octubre de 2017 |--- |
| [1.5.0](#1.5.0) |10 de agosto de 2017 |--- | 
| [1.4.1](#1.4.1) |7 de agosto de 2017 |--- |
| [1.4.0](#1.4.0) |2 de agosto de 2017 |--- |
| [1.3.2](#1.3.2) |12 de junio de 2017 |--- |
| [1.3.1](#1.3.1) |23 de mayo de 2017 |--- |
| [1.3.0](#1.3.0) |10 de mayo de 2017 |--- |
| [1.2.2](#1.2.2) |19 de abril de 2017 |--- |
| [1.2.1](#1.2.1) |29 de marzo de 2017 |--- |
| [1.2.0](#1.2.0) |25 de marzo de 2017 |--- |
| [1.1.2](#1.1.2) |20 de marzo de 2017 |--- |
| [1.1.1](#1.1.1) |14 de marzo de 2017 |--- |
| [1.1.0](#1.1.0) |16 de febrero de 2017 |--- |
| [1.0.0](#1.0.0) |21 de diciembre de 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 de noviembre de 2016 |31 de diciembre de 2016 |

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

