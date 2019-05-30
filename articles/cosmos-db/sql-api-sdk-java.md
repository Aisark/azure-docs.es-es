---
title: 'Azure Cosmos DB: SDK y recursos de Java para SQL API'
description: Obtenga toda la información sobre la API y el SDK de Java para SQL, incluidas la fechas de lanzamiento, fechas de retirada y cambios realizados entre las versiones del SDK de Java para SQL de Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 03f7f050e158ba30d2f89e79ffcc9e8723eb5268
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298684"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK de Java de Azure Cosmos DB para API de SQL: Notas de la versión y recursos
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

El SDK de Java de API de SQL admite las operaciones sincrónicas. En caso de que necesite compatibilidad asincrónica, use el [SDK de Java asincrónico de API de SQL](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Descarga del SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentación de la API**|[Documentación de referencia de API](/java/api/com.microsoft.azure.documentdb)|
|**Contribuya al SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Primeros pasos**|[Introducción al SDK de Java](sql-api-java-get-started.md)|
|**Tutorial de la aplicación web**|[Desarrollo de aplicaciones web con Azure Cosmos DB](sql-api-java-application.md)|
|**Tiempo de ejecución mínimo admitido**|[Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Se corrigió el error en PartitionKey para la versión 2 de Hash.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Se agregó compatibilidad para los índices compuestos.
* Se corrigió el error en el Administrador de punto de conexión global para forzar la actualización.
* Se corrigió el error para upserts con las condiciones previas en modo directo.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Se corrigió el error en la caché de la dirección de puerta de enlace.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Se ha agregado compatibilidad de la escritura en varias regiones con el modo directo.
* Se agregó compatibilidad para controlar excepciones de E/S producidas como excepciones ServiceUnavailable desde un proxy.
* Se ha corregido un error en la directiva de reintentos de detección de puntos de conexión.
* Se ha corregido un error para asegurarse que no se producen excepciones de puntero nulo en BaseDatabaseAccountConfigurationProvider.
* Se ha corregido un error para garantizar que queryiterator no devuelven valores NULL.
* Se ha corregido un error para asegurarse de que se permite un valor de PartitionKey grande

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Se ha agregado compatibilidad de la escritura en varias regiones con el modo de puerta de enlace.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Se ha corregido un error en los intervalos de claves de la partición de lectura en una consulta.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Se ha corregido un error en la configuración del tamaño de encabezado del token de continuación en modo DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Se ha agregado soporte técnico para conmutar por error con streaming.
* Se ha agregado compatibilidad con metadatos personalizados.
* Se ha mejorado la lógica de control de sesión.
* Se ha corregido un error en la memoria caché de intervalo de claves de partición.
* Se ha corregido un error NPE en modo directo.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Se agregó compatibilidad para índices únicos.
* Se agregó compatibilidad para limitar el tamaño del token de continuación en opciones de fuente.
* Se ha corregido un error en la serialización de Json (marca de tiempo).
* Se ha corregido un error en la serialización de Json (enumeración).
* Se ha actualizado la dependencia en com.fasterxml.jackson.core:jackson-databind a la 2.9.5.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Se ha mejorado la agrupación de conexiones para el modo directo.
* Se ha mejorado la captura previa para consultas no ordenadas entre particiones.
* Se ha mejorado la generación de UUID.
* Se ha mejorado la lógica de coherencia de sesión.
* Se agregó compatibilidad con multipolygon.
* Se agregó compatibilidad con las estadísticas de alcance clave de partición para recopilación.
* Se ha corregido un error en la compatibilidad de varias regiones.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Se ha mejorado el rendimiento de serialización de JSON.
* Esta versión del SDK requiere la versión más reciente del emulador de Azure Cosmos DB disponible para su descarga desde https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Se han realizado cambios internos en las bibliotecas de amigos de Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Se ha corregido un problema al leer intervalos de claves de partición únicos.
* Se ha corregido un problema en el análisis de ResourceID que afecta a la base de datos con nombres cortos.
* Se ha corregido un problema causado por la codificación de claves de partición.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Correcciones de errores críticos solicitará el procesamiento durante la división en particiones.
* Se ha corregido un problema con los niveles de coherencia seguro y BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Se agregó compatibilidad con un nuevo nivel de coherencia denominado ConsistentPrefix.
* Se ha corregido un error en la lectura de la colección en modo de sesión.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Habilitada compatibilidad para las colecciones particionadas con al menos 2500 RU/s y aumentos de 100 RU/s.
* Se ha corregido un error en el ensamblado nativo que puede dar lugar a la excepción NullRef en algunas consultas.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Se ha corregido un error en la configuración del motor de consulta que puede provocar excepciones para las consultas en modo de puerta de enlace.
* Se han corregido algunos errores en el contenedor de sesiones que pueden provocar una excepción "Recurso de propietario no encontrado" para las solicitudes inmediatamente después de la creación de la colección.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG). Consulte [Compatibilidad con agregación](how-to-sql-query.md#Aggregates).
* Compatibilidad agregada para cambiar la fuente.
* Compatibilidad agregada para la recopilación de la información de cuota mediante RequestOptions.setPopulateQuotaInfo.
* Compatibilidad agregada para el registro de scripts de procedimiento almacenados mediante RequestOptions.setScriptLoggingEnabled.
* Se ha corregido un error donde la consulta en modo DirectHttps puede dejar de responder cuando se producen errores de limitación.
* Se ha corregido un error en modo de sesión de coherencia.
* Se ha corregido un error que puede causar una excepción NullReferenceException en HttpContext cuando la tasa de solicitudes es alta.
* Rendimiento mejorado del modo DirectHttps.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Con la API ConnectionPolicy.setProxy(), se ha agregado compatibilidad simple con proxy basada en instancias del cliente.
* Se ha agregado la API DocumentClient.close() para cerrar correctamente la instancia de DocumentClient.
* Rendimiento de consultas mejorado en modo de conectividad directa al derivar el plan de consulta desde el ensamblado nativo, en lugar de hacerlo desde la puerta de enlace.
* Se ha establecido FAIL_ON_UNKNOWN_PROPERTIES = false para que los usuarios no necesiten definir JsonIgnoreProperties en su POJO.
* Se ha refactorizado el registro para que use SLF4J.
* Se han corregido otros errores en el lector de coherencia.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Se ha corregido un error en la administración de conexiones para evitar pérdidas de conexión en el modo de conectividad directa.
* Se ha corregido un error en la consulta TOP que puede generar una excepción NullReference.
* Se ha mejorado el rendimiento al reducir el número de llamadas de red a las memorias caché internas.
* Se ha agregado código de estado, ActivityID y la URI de la solicitud en DocumentClientException para una mejor solución de problemas.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Se ha corregido un problema en la administración de las conexiones para mejorar la estabilidad.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Compatibilidad agregada con el nivel de coherencia BoundedStaleness.
* Se ha agregado compatibilidad con la conectividad directa de las colecciones particionadas.
* Se ha corregido un error al realizar consultas en una base de datos con SQL.
* Se ha corregido un error en la caché de sesión en la que el token de sesión puede no estar configurada correctamente.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Compatibilidad agregada con las consultas paralelas entre particiones.
* Se ha agregado compatibilidad con las consultas TOP y ORDER BY en las colecciones particionadas.
* Compatibilidad agregada con Coherencia fuerte.
* Se ha agregado compatibilidad con las solicitudes basadas en nombres al utilizar la conectividad directa.
* Corrección agregada para mantener la coherencia de ActivityId en todos los reintentos de solicitud.
* Se ha corregido un error relacionado con la caché de sesión al volver a crear una colección con el mismo nombre.
* Se han agregado los tipos de datos Polygon y LineString al especificar la directiva de indización de colecciones para las consultas espaciales de geovallado.
* Problemas corregidos con Java Doc para Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Se ha corregido un error en PartitionKeyDefinitionMap para almacenar en caché colecciones de partición única y no realizar solicitudes adicionales de clave de partición de búsqueda.
* Se ha corregido un error para no realizar un reintento cuando se proporcione un valor de clave de partición incorrecto.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.
* Se ha agregado compatibilidad con el reintento automático en solicitudes limitadas, con opciones para personalizar el número máximo de reintentos y el tiempo de espera máximo de reintento.  Consulte RetryOptions y ConnectionPolicy.getRetryOptions().
* Se ha dejado de utilizar el código de creación de particiones personalizado basado en IPartitionResolver. Utilice colecciones con particiones para conseguir un almacenamiento y un rendimiento más elevados.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Se ha agregado compatibilidad con la directiva de reintentos para la limitación de velocidad.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Se ha agregado compatibilidad con período de vida (TTL) para los documentos.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Se han implementado [colecciones particionadas](partition-data.md) y [niveles de rendimiento definidos por el usuario](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Se ha corregido un error en HashPartitionResolver para generar valores hash en little endian que sean consistentes con otros SDK.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Se han agregado solucionadores de particiones de hash e intervalo para ayudar con el particionamiento de las aplicaciones entre varias particiones.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementación de Upsert. Se han agregado nuevos métodos upsertXXX para admitir la característica Upsert.
* Se implementa el enrutamiento por identificador. Sin cambios en la API pública, todos los cambios son internos.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versión omitida para alinear el número de versión con otros SDK

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Compatible con índice geoespacial.
* Valida la propiedad ID para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, \, ni terminar con un espacio.
* Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementación de la directiva de indexación V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA

## <a name="release-and-retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada a Cosmos DB mediante un SDK retirado.

> [!WARNING]
> Todas las versiones **1.x** de SQL SDK para Java se retirará en **el 30 de mayo de 2020**.
> 
>

> [!WARNING]
> Todas las versiones del SDK de SQL para Java anteriores a la versión **1.0.0** se retiraron el **29 de febrero de 2016**.
> 
> 

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13 de marzo de 2018 |--- |
| [2.1.2](#2.1.2) |09 de marzo de 2018 |--- |
| [2.1.1](#2.1.1) |13 de diciembre de 2018 |--- |
| [2.1.0](#2.1.0) |20 de noviembre de 2018 |--- |
| [2.0.0](#2.0.0) |21 de septiembre de 2018 |--- |
| [1.16.4](#1.16.4) |10 de septiembre de 2018 |30 de mayo de 2020 |
| [1.16.3](#1.16.3) |09 de septiembre de 2018 |30 de mayo de 2020 |
| [1.16.2](#1.16.2) |29 de junio de 2018 |30 de mayo de 2020 |
| [1.16.1](#1.16.1) |16 de mayo de 2018 |30 de mayo de 2020 |
| [1.16.0](#1.16.0) |15 de marzo de 2018 |30 de mayo de 2020 |
| [1.15.0](#1.15.0) |14 de noviembre de 2017 |30 de mayo de 2020 |
| [1.14.0](#1.14.0) |28 de octubre de 2017 |30 de mayo de 2020 |
| [1.13.0](#1.13.0) |25 de agosto de 2017 |30 de mayo de 2020 |
| [1.12.0](#1.12.0) |11 de julio de 2017 |30 de mayo de 2020 |
| [1.11.0](#1.11.0) |10 de mayo de 2017 |30 de mayo de 2020 |
| [1.10.0](#1.10.0) |11 de marzo de 2017 |30 de mayo de 2020 |
| [1.9.6](#1.9.6) |21 de febrero de 2017 |30 de mayo de 2020 |
| [1.9.5](#1.9.5) |31 de enero de 2017 |30 de mayo de 2020 |
| [1.9.4](#1.9.4) |24 de noviembre de 2016 |30 de mayo de 2020 |
| [1.9.3](#1.9.3) |30 de octubre de 2016 |30 de mayo de 2020 |
| [1.9.2](#1.9.2) |28 de octubre de 2016 |30 de mayo de 2020 |
| [1.9.1](#1.9.1) |26 de octubre de 2016 |30 de mayo de 2020 |
| [1.9.0](#1.9.0) |03 de octubre de 2016 |30 de mayo de 2020 |
| [1.8.1](#1.8.1) |30 de junio de 2016 |30 de mayo de 2020 |
| [1.8.0](#1.8.0) |14 de junio de 2016 |30 de mayo de 2020 |
| [1.7.1](#1.7.1) |30 de abril de 2016 |30 de mayo de 2020 |
| [1.7.0](#1.7.0) |27 de abril de 2016 |30 de mayo de 2020 |
| [1.6.0](#1.6.0) |29 de marzo de 2016 |30 de mayo de 2020 |
| [1.5.1](#1.5.1) |31 de diciembre de 2015 |30 de mayo de 2020 |
| [1.5.0](#1.5.0) |04 de diciembre de 2015 |30 de mayo de 2020 |
| [1.4.0](#1.4.0) |05 de octubre de 2015 |30 de mayo de 2020 |
| [1.3.0](#1.3.0) |05 de octubre de 2015 |30 de mayo de 2020 |
| [1.2.0](#1.2.0) |05 de agosto de 2015 |30 de mayo de 2020 |
| [1.1.0](#1.1.0) |09 de julio de 2015 |30 de mayo de 2020 |
| 1.0.1 |12 de mayo de 2015 |30 de mayo de 2020 |
| [1.0.0](#1.0.0) |07 de abril de 2015 |30 de mayo de 2020 |
| 0.9.5-prelease |09 de marzo de 2015 |29 de febrero de 2016 |
| 0.9.4-prelease |17 de febrero de 2015 |29 de febrero de 2016 |
| 0.9.3-prelease |13 de enero de 2015 |29 de febrero de 2016 |
| 0.9.2-prelease |19 de diciembre de 2014 |29 de febrero de 2016 |
| 0.9.1-prelease |19 de diciembre de 2014 |29 de febrero de 2016 |
| 0.9.0-prelease |10 de diciembre de 2014 |29 de febrero de 2016 |

## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

