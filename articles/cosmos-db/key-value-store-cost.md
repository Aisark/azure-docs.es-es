---
title: Cargos de unidades de solicitud para Azure Cosmos DB como almacén de pares de clave y valor
description: Obtenga información sobre los cargos de unidades de solicitud de Azure Cosmos DB para operaciones simples de escritura y lectura cuando se utiliza como un almacén de pares de clave y valor.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e81dec7d7556819e60a65a44106426c226c6223d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043306"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como almacén de pares valor-clave: Información general de costos

Azure Cosmos DB es un servicio de base de datos multimodelo globalmente distribuido para crear aplicaciones de alta disponibilidad y a gran escala fácilmente. De forma predeterminada, Azure Cosmos DB indexa automática y eficazmente todos los datos que ingiere. Esto permite consultas [SQL](how-to-sql-query.md) (y [JavaScript](stored-procedures-triggers-udfs.md)) rápidas y coherentes en todos los tipos de datos. 

En este artículo se describe el costo de Azure Cosmos DB para operaciones simples de escritura y lectura cuando se utiliza como un almacén de pares valor-clave. Las operaciones de escritura incluyen inserciones, reemplazos, eliminaciones y upserts de documentos. Además de garantizar un Acuerdo de Nivel de Servicio del 99,99 % de disponibilidad para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, y un 99,999 % de disponibilidad de lectura en todas las cuentas de base de datos de varias regiones, Azure Cosmos DB garantiza menos de 10 ms de latencia para lecturas y menos de 15 ms de latencia para escrituras (indizadas), respectivamente, en el percentil 99. 

## <a name="why-we-use-request-units-rus"></a>¿Por qué usamos unidades de solicitud (RU)?

El rendimiento de Azure Cosmos DB se basa en la cantidad de [unidades de solicitud](request-units.md) (RU) aprovisionadas para la partición. El aprovisionamiento tiene una granularidad de un segundo y se adquiere en RU/s ([no se debe confundir con la facturación horaria](https://azure.microsoft.com/pricing/details/cosmos-db/)). Las RU deben considerarse una moneda que simplifica el aprovisionamiento de rendimiento necesario para la aplicación. Nuestros clientes no tienen que pensar en diferenciar entre unidades de capacidad de lectura y escritura. El modelo de moneda única de RU crea eficiencias para compartir la capacidad aprovisionada entre lecturas y escrituras. Este modelo de capacidad aprovisionada permite que el servicio proporcione un rendimiento predecible y coherente, baja latencia garantizada y alta disponibilidad. Por último, utilizamos RU para modelar el rendimiento, pero cada RU aprovisionada también tiene una cantidad definida de recursos (memoria y núcleos). RU/s no es solo E/S por segundo.

Como un sistema de base de datos distribuido globalmente, Cosmos DB es el único servicio de Azure que proporciona un Acuerdo de Nivel de Servicio sobre latencia, rendimiento y coherencia además de alta disponibilidad. El rendimiento que se aprovisiona se aplica a cada una de las regiones asociadas a su cuenta de base de datos de Cosmos DB. Para lecturas, Cosmos DB ofrece varios [niveles de coherencia](consistency-levels.md) bien definidos entre los que elegir. 

En la tabla siguiente se muestra el número de RU necesarias para realizar transacciones de lectura y escritura basadas en el tamaño de documento de 1 KB y 100 KB.

|Tamaño del elemento|1 lectura|1 escritura|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>Costo de lecturas y escrituras

Si aprovisiona 1 000 RU/s, esto asciende a 3,6 millones de RU/hora y costará 0,08 USD la hora (en Estados Unidos y Europa). Para un documento de 1 KB de tamaño, esto significa que puede consumir 3,6 millones de lecturas o 0,72 millones de escrituras (3,6 millones de RU / 5) con el rendimiento aprovisionado. Normalizado a millones de lecturas y escrituras, el costo sería de 0,022 USD/millones de lecturas (0,08 USD/3,6) y 0,111/millones de escrituras (0,08 USD/0,72). El costo por millón pasa a ser mínimo tal como se muestra en la tabla siguiente.

|Tamaño del elemento|1 millón de lecturas|1 millón de escrituras|
|-------------|-------|--------|
|1 KB|0,022 USD|0,111 USD|
|100 KB|0,222 USD|1,111 USD|


La mayoría de los almacenes de blobs u objetos básicos cobran 0,40 USD por cada millón de transacciones de lectura y 5 USD por cada millón de transacciones de escritura. Si se usa de forma óptima, Cosmos DB puede ser hasta un 98 % más económico que estas otras soluciones (para transacciones de 1 KB).

## <a name="next-steps"></a>Pasos siguientes

Permanezca atento para buscar nuevos artículos sobre la optimización del aprovisionamiento de recursos de Azure Cosmos DB. Mientras tanto, no dude en usar nuestra [calculadora de RU](https://www.documentdb.com/capacityplanner).

