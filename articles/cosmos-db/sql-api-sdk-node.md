---
title: 'Azure Cosmos DB: API de SQL para Node.js, SDK y recursos'
description: Obtenga toda la información sobre la API y el SDK de Node.js para SQL incluidas la fechas de lanzamiento, fechas de retirada y cambios realizados entre las versiones del SDK de Node.js para Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 1cb6889305e5f6bce5728039712a1834dc2e9353
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626747"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SDK de Azure Cosmos DB para .NET para SQL API: Notas de la versión y recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Fuente de cambios de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor: .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor: Java](sql-api-sdk-bulk-executor-java.md)

|Recurso  |Vínculo  |
|---------|---------|
|Descargar SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentación de la API  |  [Documentación de referencia del SDK de JavaScript](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instrucciones de instalación del SDK  |  [Instrucciones de instalación](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuya al SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Ejemplos | [Ejemplos de código Node.js](sql-api-nodejs-samples.md)
| Tutorial de inicio | [Introducción al SDK de JavaScript](sql-api-nodejs-get-started.md)
| Tutorial de la aplicación web | [Creación de una aplicación web de Node.js con Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma admitida actualmente | [Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/): requerido para la versión 2.0.0 del SDK y versiones posteriores.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Notas de la versión

### <a name="2.0.5"/>2.0.5</a>
* Agrega la interfaz para el tipo de agente de nodo. Los usuarios de Typescript ya no tendrán que instalar @types/node como dependencia.
* Ahora se respetan debidamente las ubicaciones preferidas.
* Mejoras en la documentación para desarrolladores colaboradores.
* Varias correcciones de errores de escritura.

### <a name="2.0.4"/>2.0.4</a>
* Corrige el problema de definición de tipo detectado en 2.0.3.

### <a name="2.0.3"/>2.0.3</a>
* Se quita la dependencia `big-integer`.
* Se cambia a directivas de referencia para el tipo AsyncIterable. Los usuarios de Typescript ya no tienen que personalizar su configuración de "lib".
* Correcciones de errores de escritura.

### <a name="2.0.2"/>2.0.2</a>
* Se corrigen vínculos de archivo Léame.

### <a name="2.0.1"/>2.0.1</a>
* Se corrige la implementación de la interfaz de reintento.

### <a name="2.0.0"/>2.0.0</a>
* La disponibilidad general de la versión 2.0.0 del SDK de JavaScript
* Se agregó compatibilidad para escrituras en varias regiones.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 de versión 2.0.0 del SDK de JavaScript de la versión preliminar pública.
* Nuevo modelo de objetos, con CosmosClient de nivel superior y métodos divididos entre las clases pertinentes Database, Container e Item. 
* Compatibilidad con [promises](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK se convierte en TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* Documentación de NPM corregida.

### <a name="1.14.3"/>1.14.3</a>
* Compatibilidad agregada para reintentos predeterminados en los problemas de conexión.
* Compatibilidad agregada para leer la fuente de cambios de la colección.
* Error de coherencia con la sesión corregido que de vez en cuando generó el mensaje "sesión de lectura no disponible".
* Compatibilidad agregada para métricas de consulta.
* Número máximo de conexiones modificado del Agente de HTTP.

### <a name="1.14.2"/>1.14.2</a>
* Documentación actualizada para la referencia de Microsoft Azure Cosmos DB en lugar de Azure DocumentDB.
* Se agregó compatibilidad para la configuración de proxyUrl en ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Revisión secundaria para sistemas de archivos con distinción de mayúsculas y minúsculas.

### <a name="1.14.0"/>1.14.0</a>
* Agrega compatibilidad para la coherencia de la sesión.
* Esta versión del SDK requiere la versión más reciente del emulador de Azure Cosmos DB disponible para su descarga desde https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Consultas entre particiones a prueba de divisiones.
* Agrega compatibilidad para vínculos de recursos con barras diagonales iniciales y finales (y sus correspondientes pruebas).

### <a name="1.12.2"/>1.12.2</a>
*   Documentación de NPM corregida.

### <a name="1.12.1"/>1.12.1</a>
* Se ha corregido un error en executeStoredProcedure en el que los documentos implicados tenían caracteres especiales de Unicode (LS, PS).
* Se ha corregido un error a la hora de administrar los documentos con caracteres Unicode en la clave de partición.
* Se ha corregido el problema de compatibilidad con la creación de colecciones con los medios de nombre. Problema de GitHub n.º 114.
* Se ha corregido el problema de compatibilidad con el token de autorización de permiso. Problema de GitHub n.º 178.

### <a name="1.12.0"/>1.12.0</a>
* Se agregó compatibilidad con un nuevo [nivel de coherencia](consistency-levels.md) denominado ConsistentPrefix.
* Se agregó compatibilidad con UriFactory.
* Se ha corregido un error de compatibilidad con Unicode. Problema de GitHub n.º 171.

### <a name="1.11.0"/>1.11.0</a>
* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG).
* Se agregó la opción para controlar el grado de paralelismo de las consultas de partición cruzada.
* Se agregó la opción para deshabilitar la comprobación de SSL cuando se ejecuta en el emulador de Azure Cosmos DB.
* Reducción del procesamiento mínimo en las colecciones particionadas de 10 100 RU/s a 2500 RU/s.
* Se ha corregido el error de token de continuación de la colección de una sola partición. Problema de GitHub n.º 107.
* Se ha corregido el error executeStoredProcedure en el control 0 como parámetro único. Problema de GitHub n.º 155.

### <a name="1.10.2"/>1.10.2</a>
* Encabezado de agente de usuario fijo para incluir la versión del SDK.
* Limpieza menor de código.

### <a name="1.10.1"/>1.10.1</a>
* Se ha deshabilitado la comprobación de SSL cuando se usa el SDK para alcanzar el emulador (nombre de host = host local).
* Se ha agregado compatibilidad para habilitar el registro de scripts durante la ejecución de procedimientos almacenados.

### <a name="1.10.0"/>1.10.0</a>
* Compatibilidad agregada con las consultas paralelas entre particiones.
* Se ha agregado compatibilidad con las consultas TOP y ORDER BY en las colecciones particionadas.

### <a name="1.9.0"/>1.9.0</a>
* Se ha agregado compatibilidad de la directiva de reintentos con las solicitudes de limitación. (Las solicitudes limitadas reciben una excepción demasiado grande de la tasa de solicitudes, código de error 429). De manera predeterminada, Azure Cosmos DB realiza nueve reintentos para cada solicitud cuando aparece el código de error 429, respetando el tiempo de retryAfter en el encabezado de respuesta. Ahora puede establecerse un tiempo del intervalo de reintento fijo como parte de la propiedad RetryOptions del objeto ConnectionPolicy si quiere ignorar el tiempo de retryAfter que ha devuelto el servidor entre los reintentos. Azure Cosmos DB espera ahora un máximo de 30 segundos para cada solicitud que se está limitando (independientemente del recuento de reintentos) y devuelve la respuesta con el código de error 429. Este tiempo también puede reemplazarse en la propiedad RetryOptions del objeto ConnectionPolicy.
* Cosmos DB ahora devuelve x-ms-throttle-retry-count y x-ms-throttle-retry-wait-time-ms como los encabezados de respuesta de cada solicitud para denotar el recuento de reintentos de limitación y el tiempo acumulativo que ha esperado la solicitud entre los reintentos.
* Se ha agregado la clase RetryOptions que expone la propiedad RetryOptions en la clase ConnectionPolicy que puede usarse para reemplazar algunas de las opciones de reintentos predeterminadas.

### <a name="1.8.0"/>1.8.0</a>
* Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.

### <a name="1.7.0"/>1.7.0</a>
* Se ha agregado compatibilidad con la característica de período de vida (TTL) para los documentos.

### <a name="1.6.0"/>1.6.0</a>
* Se han implementado [colecciones particionadas](partition-data.md) y [niveles de rendimiento definidos por el usuario](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Solucionado el error RangePartitionResolver.resolveForRead por el que no se devolvían vínculos debido a una mala concatenación de resultados.

### <a name="1.5.5"/>1.5.5</a>
* Valor hashParitionResolver resolveForRead() corregido: cuando ninguna clave de partición proporcionada ha lanzado una excepción, en lugar de devolver una lista de todos los vínculos registrados.

### <a name="1.5.4"/>1.5.4</a>
* Corrige el problema [100](https://github.com/Azure/azure-documentdb-node/issues/100) -agente específico de HTTPS: evite la modificación del agente global para fines de Azure Cosmos DB. Use un agente dedicado para todas las solicitudes de lib.

### <a name="1.5.3"/>1.5.3</a>
* Corrige el problema [81](https://github.com/Azure/azure-documentdb-node/issues/81) : controla correctamente guiones en identificadores de medios.

### <a name="1.5.2"/>1.5.2</a>
* Corrige el problema [95](https://github.com/Azure/azure-documentdb-node/issues/95) : advertencia de pérdida de escucha de EventEmitter.

### <a name="1.5.1"/>1.5.1</a>
* Corrige el problema [92](https://github.com/Azure/azure-documentdb-node/issues/90): cambio de nombre de la carpeta Hash a hash para sistemas que distinguen mayúsculas de minúsculas.

### <a name="1.5.0"/>1.5.0</a>
* Se implementa la compatibilidad con el particionamiento, para lo que se agregan resolvedores de hash y de particiones de intervalo.

### <a name="1.4.0"/>1.4.0</a>
* Implementación de Upsert. Se han agregado nuevos métodos upsertXXX en documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Omitida para alinear el número de versión con otros SDK.

### <a name="1.2.2"/>1.2.2</a>
* Divide el contenedor de objetos Q promise en nuevo repositorio.
* Actualización del archivo de paquete para registro npm.

### <a name="1.2.1"/>1.2.1</a>
* Se implementa el enrutamiento por identificador.
* Corrige el problema [49](https://github.com/Azure/azure-documentdb-node/issues/49) : la propiedad current entra en conflicto con el método current().

### <a name="1.2.0"/>1.2.0</a>
* Se agregó compatibilidad con índice geoespacial.
* Valida la propiedad id para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, &#47;&#47;, ni terminar con un espacio.
* Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementación de la directiva de indexación V2.

### <a name="1.0.3"/>1.0.3</a>
* Problema [40](https://github.com/Azure/azure-documentdb-node/issues/40) : se implementaron configuraciones eslint y grunt en el núcleo y el SDK de promise.

### <a name="1.0.2"/>1.0.2</a>
* Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45) : el contenedor de objetos promise no incluye el encabezado con el error.

### <a name="1.0.1"/>1.0.1</a>
* Implementación de capacidad para consultar conflictos agregando readConflicts, readConflictAsync y queryConflicts.
* Documentación de la API actualizada.
* Problema [41](https://github.com/Azure/azure-documentdb-node/issues/41) : error client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* SDK de GA.

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notifica la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada a Cosmos DB mediante un SDK retirado.

<br/>

| `Version` | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 de agosto de 2018 |--- |
| [1.14.4](#1.14.4) |3 de mayo de 2018 |--- |
| [1.14.3](#1.14.3) |3 de mayo de 2018 |--- |
| [1.14.2](#1.14.2) |21 de diciembre de 2017 |--- |
| [1.14.1](#1.14.1) |10 de noviembre de 2017 |--- |
| [1.14.0](#1.14.0) |9 de noviembre de 2017 |--- |
| [1.13.0](#1.13.0) |11 de octubre de 2017 |--- |
| [1.12.2](#1.12.2) |10 de agosto de 2017 |--- |
| [1.12.1](#1.12.1) |10 de agosto de 2017 |--- |
| [1.12.0](#1.12.0) |10 de mayo de 2017 |--- |
| [1.11.0](#1.11.0) |16 de marzo de 2017 |--- |
| [1.10.2](#1.10.2) |27 de enero de 2017 |--- |
| [1.10.1](#1.10.1) |22 de diciembre de 2016 |--- |
| [1.10.0](#1.10.0) |03 de octubre de 2016 |--- |
| [1.9.0](#1.9.0) |7 de julio de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junio de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de marzo de 2016 |--- |
| [1.5.6](#1.5.6) |8 de marzo de 2016 |--- |
| [1.5.5](#1.5.5) |2 de febrero de 2016 |--- |
| [1.5.4](#1.5.4) |1 de febrero de 2016 |--- |
| [1.5.2](#1.5.2) |26 de enero de 2016 |--- |
| [1.5.2](#1.5.2) |22 de enero de 2016 |--- |
| [1.5.1](#1.5.1) |4 de enero de 2016 |--- |
| [1.5.0](#1.5.0) |31 de diciembre de 2015 |--- |
| [1.4.0](#1.4.0) |6 de octubre de 2015 |--- |
| [1.3.0](#1.3.0) |6 de octubre de 2015 |--- |
| [1.2.2](#1.2.2) |10 de septiembre de 2015 |--- |
| [1.2.1](#1.2.1) |15 de agosto de 2015 |--- |
| [1.2.0](#1.2.0) |05 de agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de julio de 2015 |--- |
| [1.0.3](#1.0.3) |4 de junio de 2015 |--- |
| [1.0.2](#1.0.2) |23 de mayo de 2015 |--- |
| [1.0.1](#1.0.1) |15 de mayo de 2015 |--- |
| [1.0.0](#1.0.0) |08 de abril de 2015 |--- |

## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

