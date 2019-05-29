---
title: Introducción a Table API de Azure Cosmos DB
description: Aprenda a usa Azure Cosmos DB para almacenar y consultar grandes volúmenes de datos de pares clave-valor con latencia baja mediante instancias de Table API de Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: bd86b230d801f5fff8a9fb0de85f9f3025527382
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953456"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introducción a Azure Cosmos DB: Table API

[Azure Cosmos DB](introduction.md) proporciona Table API para aplicaciones escritas para Azure Table Storage y que necesitan funcionalidades premium como:

* [Distribución global inmediata](distribute-data-globally.md).
* [Rendimiento dedicado](partition-data.md) en todo el mundo.
* Latencias en milisegundos de un solo dígito en el percentil 99.
* Alta disponibilidad garantizada.
* [Indexación secundaria automática](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Las aplicaciones escritas para Azure Table Storage pueden migrarse a Azure Cosmos DB mediante la API Table sin realizar ningún cambio en el código y pueden sacar provecho de las funcionalidades premium. Table API tiene SDK de cliente disponibles para .NET, Java, Python y Node.js.

> [!IMPORTANT]
> El SDK de .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) se encuentra en modo de mantenimiento y dejará de utilizarse pronto. Actualice a la nueva biblioteca de .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) para continuar recibiendo las últimas características compatibles con Table API.

## <a name="table-offerings"></a>Ofertas de Table
Si actualmente usa Azure Table Storage, obtendrá las siguientes ventajas al actualizar a Table API de Azure Cosmos DB:

| | Almacenamiento de tablas de Azure | Table API de Azure Cosmos DB |
| --- | --- | --- |
| Latencia | Rápido, pero no hay límites máximos en la latencia. | Latencia en milisegundos de un solo dígito para lecturas y escrituras, respaldada con lecturas con una latencia inferior a 10 ms y escrituras con una latencia inferior a 15 ms en el percentil 99, a cualquier escala, en cualquier lugar del mundo. |
| Throughput | Modelo de rendimiento variable. Las tablas tienen un límite de escalabilidad de 20.000 operaciones por segundo. | Altamente escalable con [rendimiento reservado dedicado por tabla](request-units.md) respaldado por los SLA. Las cuentas no tienen límite máximo en el rendimiento y admiten más de 10 millones de operaciones por segundo por tabla. |
| Distribución global | Una sola región, con una región de lectura secundaria legible opcional para alta disponibilidad. No se puede iniciar la conmutación por error. | [Distribución global inmediata](distribute-data-globally.md) desde 1 a más de 30 regiones. Admite [conmutaciones por error automáticas y manuales](high-availability.md) en cualquier momento y en cualquier lugar del mundo. |
| Indización | Índice principal solo en PartitionKey y RowKey. No hay índices secundarios. | Indexación automática y completa en todas las propiedades, sin administración de índices. |
| Consultar | La ejecución de consultas usa el índice de la clave principal y, en caso contrario, examina. | Las consultas pueden aprovechar la indexación automática en las propiedades para reducir el tiempo de consulta. |
| Coherencia | Seguro dentro de la región principal. Ocasional en la región secundaria. | [Cinco niveles de coherencia bien definidos](consistency-levels.md) para compensar la disponibilidad, la latencia, el rendimiento y la coherencia en función de las necesidades de la aplicación. |
| Precios | Optimizado para el almacenamiento. | Optimizado para el rendimiento. |
| SLA | disponibilidad del 99,99%. | Acuerdo de Nivel de Servicio con disponibilidad del 99,99 % para todas las cuentas de región individual y todas las cuentas de varias regiones con coherencia menos estricta y disponibilidad del 99,999 % para todos los [Acuerdos de Nivel de Servicio completos líderes en el sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de todas las cuentas de base de datos de varias regiones de lectura sobre la disponibilidad general. |

## <a name="get-started"></a>Introducción

Crear una cuenta de Azure Cosmos DB en [Azure Portal](https://portal.azure.com). A continuación, empezar a trabajar con nuestra [Guía de inicio rápido para la API Table mediante .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Si ha creado una cuenta de Table API durante la versión preliminar, cree una [nueva cuenta de Table API](create-table-dotnet.md#create-a-database-account) para trabajar con los SDK de Table API disponibles con carácter general.
>

## <a name="next-steps"></a>Pasos siguientes

Estas son algunas sugerencias para comenzar:
* [Creación de una aplicación .NET con la API Table](create-table-dotnet.md)
* [Azure Cosmos DB: desarrollo con Table API en .NET](tutorial-develop-table-dotnet.md)
* [Azure Cosmos DB: instrucciones de realización de consultas de tablas de datos con Table API (versión preliminar)](tutorial-query-table.md)
* [Configuración de la distribución global de Azure Cosmos DB con la API Table](tutorial-global-distribution-table.md)
* [API Table de .NET de Azure Cosmos DB](table-sdk-dotnet.md)
* [Table API de Azure Cosmos DB para Java](table-sdk-java.md)
* [Table API de Azure Cosmos DB para Node.js](table-sdk-nodejs.md)
* [SDK de Table de Azure Cosmos DB para Python](table-sdk-python.md)

